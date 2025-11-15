# Email Validation and Verification

## Installation

```bash
# Python
pip install email-validator dnspython

# Node.js
npm install email-validator
```

## Python Email Validation

```python
# email_validator.py
import re
from email_validator import validate_email, EmailNotValidError
import dns.resolver
import socket
import smtplib
from typing import Dict, Tuple

class EmailValidator:
    @staticmethod
    def validate_format(email: str) -> Tuple[bool, str]:
        """Validate email format"""
        try:
            valid = validate_email(email)
            return True, valid.email
        except EmailNotValidError as e:
            return False, str(e)

    @staticmethod
    def check_mx_records(email: str) -> bool:
        """Check MX records for domain"""
        try:
            domain = email.split('@')[1]
            mx_records = dns.resolver.resolve(domain, 'MX')
            return len(mx_records) > 0
        except Exception:
            return False

    @staticmethod
    def check_server_exists(email: str) -> bool:
        """Check if email server exists via SMTP"""
        try:
            domain = email.split('@')[1]
            mx_records = dns.resolver.resolve(domain, 'MX')
            mx_host = str(mx_records[0].exchange)
            
            # Try to connect to SMTP server
            server = smtplib.SMTP(timeout=10)
            server.connect(mx_host, 25)
            server.quit()
            return True
        except Exception:
            return False

    @staticmethod
    def is_disposable_email(email: str) -> bool:
        """Check if email is from disposable email provider"""
        domain = email.split('@')[1].lower()
        disposable_domains = [
            '10minutemail.com', 'guerrillamail.com', 'mailinator.com',
            'tempmail.org', 'throwaway.email', 'yopmail.com'
        ]
        return domain in disposable_domains

    @staticmethod
    def is_role_based_email(email: str) -> bool:
        """Check if email is role-based"""
        local = email.split('@')[0].lower()
        role_emails = [
            'info', 'admin', 'support', 'contact', 'sales', 'team',
            'noreply', 'no-reply', 'service', 'help', 'hello'
        ]
        return local in role_emails

    @staticmethod
    def validate_email_comprehensive(email: str) -> Dict:
        """Comprehensive email validation"""
        result = {'email': email, 'valid': False, 'checks': {}}
        
        # Format validation
        is_valid, message = EmailValidator.validate_format(email)
        result['checks']['format'] = {'valid': is_valid, 'message': message}
        
        if not is_valid:
            result['reason'] = 'Invalid format'
            return result
        
        # MX record check
        has_mx = EmailValidator.check_mx_records(email)
        result['checks']['mx_records'] = {'valid': has_mx}
        
        if not has_mx:
            result['reason'] = 'Domain has no MX records'
            return result
        
        # Server existence check (optional, slower)
        server_exists = EmailValidator.check_server_exists(email)
        result['checks']['server_exists'] = {'valid': server_exists}
        
        # Disposable email check
        is_disposable = EmailValidator.is_disposable_email(email)
        result['checks']['disposable'] = {'valid': not is_disposable}
        
        # Role-based email check
        is_role_based = EmailValidator.is_role_based_email(email)
        result['checks']['role_based'] = {'is_role_based': is_role_based}
        
        # Overall validity
        result['valid'] = has_mx and not is_disposable
        
        return result

    @staticmethod
    def generate_verification_token(length: int = 32) -> str:
        """Generate email verification token"""
        import secrets
        return secrets.token_urlsafe(length)

    @staticmethod
    def verify_token(token: str, stored_tokens: Dict) -> bool:
        """Verify email verification token"""
        return token in stored_tokens and stored_tokens[token]['expires'] > datetime.now()
```

## Node.js Email Validation

```javascript
// emailValidator.js
const validator = require('email-validator');
const dns = require('dns').promises;
const disposableDomains = [
  '10minutemail.com', 'guerrillamail.com', 'mailinator.com',
  'tempmail.org', 'throwaway.email', 'yopmail.com'
];

const roleEmails = [
  'info', 'admin', 'support', 'contact', 'sales', 'team',
  'noreply', 'no-reply', 'service', 'help', 'hello'
];

class EmailValidator {
  static validateFormat(email) {
    return validator.validate(email);
  }

  static async checkMxRecords(email) {
    try {
      const domain = email.split('@')[1];
      const addresses = await dns.resolveMx(domain);
      return addresses.length > 0;
    } catch (error) {
      return false;
    }
  }

  static isDisposable(email) {
    const domain = email.split('@')[1].toLowerCase();
    return disposableDomains.includes(domain);
  }

  static isRoleBased(email) {
    const local = email.split('@')[0].toLowerCase();
    return roleEmails.includes(local);
  }

  static async validateEmailComprehensive(email) {
    const result = {
      email,
      valid: false,
      checks: {}
    };

    // Format validation
    const isValidFormat = this.validateFormat(email);
    result.checks.format = { valid: isValidFormat };
    
    if (!isValidFormat) {
      result.reason = 'Invalid format';
      return result;
    }

    // MX record check
    const hasMx = await this.checkMxRecords(email);
    result.checks.mx = { valid: hasMx };
    
    if (!hasMx) {
      result.reason = 'Domain has no MX records';
      return result;
    }

    // Disposable check
    const isDisposable = this.isDisposable(email);
    result.checks.disposable = { valid: !isDisposable };

    // Role-based check
    const isRoleBased = this.isRoleBased(email);
    result.checks.roleBased = { isRoleBased };

    // Overall validity
    result.valid = hasMx && !isDisposable;

    return result;
  }
}

module.exports = EmailValidator;
```

## Integration with Email Service

### Python Integration

```python
# enhanced_email_service.py
from email_validator import EmailValidator
from typing import Optional

class EnhancedEmailService:
    def __init__(self):
        self.validator = EmailValidator()

    async def send_verification_email(
        self,
        email: str,
        background_tasks: BackgroundTasks = None
    ) -> Dict:
        """Send verification email with validation"""
        
        # First validate email
        validation_result = self.validator.validate_email_comprehensive(email)
        
        if not validation_result['valid']:
            return {
                'success': False,
                'reason': validation_result.get('reason', 'Invalid email'),
                'validation': validation_result
            }
        
        # Generate verification token
        verification_token = self.validator.generate_verification_token()
        
        # Send email
        success = await self._send_verification_email(email, verification_token, background_tasks)
        
        if success:
            # Store token in database
            await VerificationToken.create(email=email, token=verification_token)
            return {
                'success': True,
                'message': 'Verification email sent',
                'validation': validation_result
            }
        else:
            return {
                'success': False,
                'reason': 'Failed to send email',
                'validation': validation_result
            }

    async def send_welcome_email(
        self,
        email: str,
        name: str,
        background_tasks: BackgroundTasks = None
    ) -> Dict:
        """Send welcome email with validation"""
        
        validation_result = self.validator.validate_email_comprehensive(email)
        
        if not validation_result['valid']:
            return {
                'success': False,
                'reason': validation_result.get('reason', 'Invalid email'),
                'validation': validation_result
            }
        
        success = await self._send_welcome_email(email, name, background_tasks)
        
        return {
            'success': success,
            'message': 'Welcome email sent' if success else 'Failed to send email',
            'validation': validation_result
        }
```

### Node.js Integration

```javascript
// enhancedEmailService.js
const EmailValidator = require('./emailValidator');
const EmailService = require('./emailService');

class EnhancedEmailService {
  constructor() {
    this.validator = EmailValidator;
    this.emailService = new EmailService();
  }

  async sendVerificationEmail(email, backgroundTasks = null) {
    try {
      // Validate email first
      const validation = await this.validator.validateEmailComprehensive(email);
      
      if (!validation.valid) {
        return {
          success: false,
          reason: validation.reason || 'Invalid email',
          validation
        };
      }

      // Generate verification token
      const verificationToken = this.generateVerificationToken();
      
      // Send email
      const result = await this.emailService.sendVerificationEmail(
        email, 
        verificationToken, 
        backgroundTasks
      );

      if (result.success) {
        // Store token in database
        await VerificationToken.create({
          email,
          token: verificationToken,
          expires: new Date(Date.now() + 24 * 60 * 60 * 1000) // 24 hours
        });
      }

      return {
        ...result,
        validation
      };
    } catch (error) {
      return {
        success: false,
        reason: error.message,
        validation: { valid: false, error: error.message }
      };
    }
  }

  generateVerificationToken(length = 32) {
    const crypto = require('crypto');
    return crypto.randomBytes(length).toString('hex');
  }
}

module.exports = EnhancedEmailService;
```

## API Validation Endpoints

### FastAPI Validation Endpoint

```python
# routes/validation.py
from fastapi import APIRouter, HTTPException
from pydantic import BaseModel, EmailStr
from email_validator import EmailValidator

router = APIRouter(prefix="/api/validation", tags=["validation"])

class EmailValidationRequest(BaseModel):
    email: EmailStr

class EmailValidationResponse(BaseModel):
    email: str
    valid: bool
    reason: Optional[str] = None
    checks: dict

@router.post("/email", response_model=EmailValidationResponse)
async def validate_email(request: EmailValidationRequest):
    """Validate email address"""
    try:
        validator = EmailValidator()
        result = validator.validate_email_comprehensive(request.email)
        return result
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))
```

### Express.js Validation Endpoint

```javascript
// routes/validation.js
const express = require('express');
const router = express.Router();
const EmailValidator = require('../emailValidator');

router.post('/email', async (req, res) => {
  try {
    const { email } = req.body;
    
    if (!email) {
      return res.status(400).json({ error: 'Email is required' });
    }

    const result = await EmailValidator.validateEmailComprehensive(email);
    res.json(result);
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});

module.exports = router;
```

## Testing Email Validation

### Python Tests

```python
# test_email_validation.py
import pytest
from email_validator import EmailValidator

def test_valid_email():
    result = EmailValidator.validate_email_comprehensive('test@gmail.com')
    assert result['valid'] == True

def test_invalid_format():
    result = EmailValidator.validate_email_comprehensive('invalid-email')
    assert result['valid'] == False
    assert 'format' in result['checks']
    assert result['checks']['format']['valid'] == False

def test_disposable_email():
    result = EmailValidator.validate_email_comprehensive('test@10minutemail.com')
    assert result['valid'] == False
    assert result['checks']['disposable']['valid'] == False

def test_role_based_email():
    result = EmailValidator.validate_email_comprehensive('info@gmail.com')
    assert result['checks']['roleBased']['isRoleBased'] == True
```

### JavaScript Tests

```javascript
// testEmailValidation.js
const EmailValidator = require('./emailValidator');

async function runTests() {
  console.log('Testing email validation...');
  
  // Valid email
  const validResult = await EmailValidator.validateEmailComprehensive('test@gmail.com');
  console.log('Valid email:', validResult.valid); // Should be true
  
  // Invalid format
  const invalidResult = await EmailValidator.validateEmailComprehensive('invalid-email');
  console.log('Invalid format:', invalidResult.valid); // Should be false
  
  // Disposable email
  const disposableResult = await EmailValidator.validateEmailComprehensive('test@10minutemail.com');
  console.log('Disposable email:', disposableResult.valid); // Should be false
  
  // Role-based email
  const roleBasedResult = await EmailValidator.validateEmailComprehensive('info@gmail.com');
  console.log('Role-based email:', roleBasedResult.checks.roleBased.isRoleBased); // Should be true
}

runTests().catch(console.error);
```
