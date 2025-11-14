# Password Policy Template

Use this template to define password requirements for your organization.

---

## Standard Password Policy (Most Common)

### Requirements

```
✓ Minimum length: 12 characters
✓ Maximum length: 128 characters
✓ Character types: UPPERCASE + lowercase + numbers + special chars
  - Uppercase: A-Z (at least 1)
  - Lowercase: a-z (at least 1)
  - Numbers: 0-9 (at least 1)
  - Special: !@#$%^&*()_+-=[]{}|;:,.<>? (at least 1)
✓ No dictionary words (prevent "P@ssword123!")
✓ No username/email in password
✓ No repeating characters (max 3 consecutive: "aaa" ❌)
✓ No sequential characters (max 3 sequential: "abc" ❌)
```

### Expiration

```
✓ Expiration: 90 days
✓ Warning: 7 days before expiry
✓ Grace period: 24 hours after expiry (can still login, forced reset)
✓ History: Don't allow reuse of last 5 passwords
✓ Minimum age: 1 day (prevent password reset spam)
```

### Account Lockout

```
✓ Failed attempts before lockout: 5 attempts
✓ Observation window: 15 minutes
✓ Lockout duration: 15 minutes (or until admin unlock)
✓ Reset on successful login: yes
✓ Notification: email alert on account lock
```

### Password Reset

```
✓ Reset link expiration: 30 minutes
✓ Reset link: single-use only
✓ New password must differ: at least 4 characters different from old
✓ Confirmation: require user to enter new password twice
✓ Notification: email confirmation of password change
```

---

## Enhanced Policy (High Security)

### For admin/privileged accounts:

```
✓ Minimum length: 16 characters
✓ Expiration: 60 days (shorter)
✓ History: Don't allow reuse of last 12 passwords
✓ Multi-factor authentication: REQUIRED
✓ Failed attempts: 3 (stricter)
✓ Reset link expiration: 15 minutes
✓ Password strength: verified by password manager integration
```

### Implementation (Node.js/TypeScript)

```typescript
interface PasswordPolicy {
  minLength: number
  maxLength: number
  requireUppercase: boolean
  requireLowercase: boolean
  requireNumbers: boolean
  requireSpecialChars: boolean
  specialChars: string
  prohibitUsername: boolean
  prohibitEmail: boolean
  maxConsecutiveChars: number
  maxSequentialChars: number
  expirationDays: number
  historyCount: number
  minPasswordAge: number
}

const standardPolicy: PasswordPolicy = {
  minLength: 12,
  maxLength: 128,
  requireUppercase: true,
  requireLowercase: true,
  requireNumbers: true,
  requireSpecialChars: true,
  specialChars: '!@#$%^&*()_+-=[]{}|;:,.<>?',
  prohibitUsername: true,
  prohibitEmail: true,
  maxConsecutiveChars: 3,
  maxSequentialChars: 3,
  expirationDays: 90,
  historyCount: 5,
  minPasswordAge: 1
}

const enhancedPolicy: PasswordPolicy = {
  ...standardPolicy,
  minLength: 16,
  expirationDays: 60,
  historyCount: 12
}

/**
 * Validate password against policy
 */
function validatePassword(
  password: string,
  username: string,
  email: string,
  policy: PasswordPolicy
): { valid: boolean; errors: string[] } {
  const errors: string[] = []

  // Length check
  if (password.length < policy.minLength) {
    errors.push(`Password must be at least ${policy.minLength} characters`)
  }
  if (password.length > policy.maxLength) {
    errors.push(`Password must be no more than ${policy.maxLength} characters`)
  }

  // Character type checks
  if (policy.requireUppercase && !/[A-Z]/.test(password)) {
    errors.push('Password must contain at least one uppercase letter')
  }
  if (policy.requireLowercase && !/[a-z]/.test(password)) {
    errors.push('Password must contain at least one lowercase letter')
  }
  if (policy.requireNumbers && !/\d/.test(password)) {
    errors.push('Password must contain at least one number')
  }
  if (policy.requireSpecialChars) {
    const specialCharRegex = new RegExp(
      `[${policy.specialChars.replace(/[-[\]{}()*+?.,\\^$|#\s]/g, '\\$&')}]`
    )
    if (!specialCharRegex.test(password)) {
      errors.push(`Password must contain a special character: ${policy.specialChars}`)
    }
  }

  // Content checks
  if (policy.prohibitUsername && password.toLowerCase().includes(username.toLowerCase())) {
    errors.push('Password cannot contain your username')
  }
  if (policy.prohibitEmail && password.toLowerCase().includes(email.toLowerCase())) {
    errors.push('Password cannot contain your email address')
  }

  // Consecutive/sequential character checks
  if (policy.maxConsecutiveChars > 0) {
    const consecutivePattern = new RegExp(
      `.{${policy.maxConsecutiveChars + 1}}`
    )
    for (let i = 0; i < password.length - policy.maxConsecutiveChars; i++) {
      const char = password[i]
      let consecutive = 1
      for (let j = i + 1; j < password.length; j++) {
        if (password[j] === char) {
          consecutive++
        } else {
          break
        }
      }
      if (consecutive > policy.maxConsecutiveChars) {
        errors.push(
          `Password cannot contain more than ${policy.maxConsecutiveChars} consecutive characters`
        )
        break
      }
    }
  }

  return {
    valid: errors.length === 0,
    errors
  }
}

/**
 * Check if password is strong (UX feature, not validation)
 */
function getPasswordStrength(password: string): 'weak' | 'fair' | 'good' | 'strong' {
  let score = 0

  // Length scoring
  if (password.length >= 8) score++
  if (password.length >= 12) score++
  if (password.length >= 16) score++

  // Character type scoring
  if (/[a-z]/.test(password)) score++
  if (/[A-Z]/.test(password)) score++
  if (/\d/.test(password)) score++
  if (/[!@#$%^&*]/.test(password)) score++

  // Variety scoring
  const uniqueChars = new Set(password).size
  if (uniqueChars >= 10) score++

  // Final score
  if (score <= 2) return 'weak'
  if (score <= 4) return 'fair'
  if (score <= 6) return 'good'
  return 'strong'
}
```

---

## Organization-Specific Policies

### SaaS (B2B)

```
✓ Minimum length: 12 characters
✓ Expiration: 90 days
✓ Multi-factor authentication: Recommended (free tier), Required (paid)
✓ Account lockout: 5 attempts / 15 minutes
✓ Password reset: admin can force reset
```

### Financial/Healthcare (HIPAA/PCI)

```
✓ Minimum length: 16 characters
✓ Expiration: 60 days
✓ History: 24 previous passwords
✓ Multi-factor authentication: REQUIRED
✓ Account lockout: 3 attempts / 30 minutes
✓ Audit logging: immutable, encrypted
✓ Compliance: HIPAA BAA, PCI DSS Level 1
```

### Enterprise (NIST SP 800-63B)

```
✓ Length requirements: 8+ (or dictionary-based)
✓ Composition: no restrictions (length > complexity)
✓ Expiration: removal of mandatory expiration (use risk-based)
✓ History: no restrictions (focus on breach prevention)
✓ Multi-factor authentication: REQUIRED for privileged accounts
✓ Memorized Secret (password) guidance: complexity optional, length critical
```

---

## Frontend Implementation Example

```html
<form id="passwordForm">
  <div>
    <label for="password">Password</label>
    <input 
      type="password" 
      id="password" 
      name="password"
      placeholder="Min 12 chars, 1 uppercase, 1 number, 1 special"
    />
    <div id="strengthMeter">
      <div id="strengthBar" style="width: 0%; height: 4px; background: gray;"></div>
    </div>
    <small id="strengthText">Strength: Weak</small>
  </div>

  <div id="requirements">
    <h4>Password Requirements:</h4>
    <ul>
      <li id="req-length">✗ At least 12 characters</li>
      <li id="req-uppercase">✗ One uppercase letter</li>
      <li id="req-lowercase">✗ One lowercase letter</li>
      <li id="req-number">✗ One number</li>
      <li id="req-special">✗ One special character (!@#$...)</li>
    </ul>
  </div>

  <button type="submit" disabled>Create Account</button>
</form>

<script>
const passwordInput = document.getElementById('password')
const submitBtn = document.querySelector('button[type="submit"]')
const requirements = {
  length: { el: document.getElementById('req-length'), regex: /.{12,}/ },
  uppercase: { el: document.getElementById('req-uppercase'), regex: /[A-Z]/ },
  lowercase: { el: document.getElementById('req-lowercase'), regex: /[a-z]/ },
  number: { el: document.getElementById('req-number'), regex: /\d/ },
  special: { el: document.getElementById('req-special'), regex: /[!@#$%^&*]/ }
}

passwordInput.addEventListener('input', (e) => {
  const password = e.target.value
  let met = 0

  Object.entries(requirements).forEach(([key, { el, regex }]) => {
    if (regex.test(password)) {
      el.textContent = '✓ ' + el.textContent.substring(2)
      el.style.color = 'green'
      met++
    } else {
      el.textContent = '✗ ' + el.textContent.substring(2)
      el.style.color = 'red'
    }
  })

  // Update strength meter
  const strength = ['Weak', 'Fair', 'Good', 'Strong'][met - 1] || 'Weak'
  const colors = ['red', 'orange', 'yellow', 'green']
  const widths = [20, 40, 70, 100]

  document.getElementById('strengthText').textContent = `Strength: ${strength}`
  document.getElementById('strengthBar').style.background = colors[met - 1] || 'red'
  document.getElementById('strengthBar').style.width = (widths[met - 1] || 0) + '%'

  // Enable/disable submit
  submitBtn.disabled = met < 5
})
</script>
```

---

## Password Manager Integration

Recommend users enable password manager browser extensions:

- **1Password**
- **LastPass**
- **Dashlane**
- **Bitwarden** (open-source)

These tools:
- Generate random, complex passwords
- Store passwords securely
- Autofill login forms
- Reduce password reuse attacks

---

## Common Mistakes to Avoid

❌ **Too complex** - Users write passwords on sticky notes
✓ **Balance complexity with length** - 12 chars > 8 chars with 3 special

❌ **Too frequent expiration** - Forces poor resets
✓ **90 days is standard** - 30 days only for high-risk accounts

❌ **Security questions** - Often public or guessable
✓ **Use authenticator apps** - TOTP more secure

❌ **Storing passwords** - Plain text, salted, or weak hashing
✓ **Use bcrypt/Argon2** - Slow, memory-hard algorithms

❌ **No rate limiting** - Brute force attacks succeed
✓ **5 attempts / 15 minutes** - Standard protection

---

## Testing the Policy

```bash
# Test minimum length
curl -X POST /register -d '{"password":"short"}'
# Response: "Password must be at least 12 characters"

# Test character types
curl -X POST /register -d '{"password":"alllowercase123!"}'
# Response: "Password must contain at least one uppercase letter"

# Test username check
curl -X POST /register -d '{"username":"john","password":"John@12345"}'
# Response: "Password cannot contain your username"

# Test valid password
curl -X POST /register -d '{"password":"SecureP@ssw0rd"}'
# Response: Success
```

---

## Compliance References

- **NIST SP 800-63B**: https://pages.nist.gov/800-63-3/sp800-63b.html
- **OWASP Password Storage**: https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html
- **GDPR Data Protection**: https://gdpr-info.eu/
- **HIPAA Security Rule**: https://www.hhs.gov/hipaa/for-professionals/security/index.html
- **PCI DSS Password Requirements**: https://www.pcisecuritystandards.org/
