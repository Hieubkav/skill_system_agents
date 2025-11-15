---
name: email-service-integration
description: Tích hợp email service với backend sử dụng SMTP, third-party providers, templates và async sending. Dùng khi implement email functionality, gửi transactional emails và quản lý email workflows.
---

# Email Service Integration

## Level 1: Overview

Skill này giúp tích hợp email services vào backend applications với SMTP, third-party providers (SendGrid, Mailgun, AWS SES), HTML templates, email validation, và async sending. Hỗ trợ implement welcome emails, password reset, verification emails và bulk campaigns.

### Use Cases
- Gửi transactional emails (welcome, password reset, verification)
- Implement email notifications
- Tạo email templates với MJML
- Background email processing
- Email validation và verification

### Requirements
- Backend framework (Flask, FastAPI, Express.js)
- Email provider credentials
- SMTP access hoặc third-party service API keys

### Output
- Complete email service implementation
- Production-ready email templates
- Background email processing
- Email validation system

## Level 2: Quick Start

### Basic Implementation Steps

```bash
# Python - Install dependencies
pip install flask-mail fastapi-mail email-validator

# Node.js - Install dependencies  
npm install @sendgrid/mail email-validator
```

### Quick Flask/SMTP Example

```python
from flask_mail import Mail, Message
mail = Mail()

class EmailService:
    def send_welcome_email(self, user_email, user_name):
        msg = Message(
            "Welcome!",
            recipients=[user_email],
            html=f"<h1>Welcome, {user_name}!</h1>"
        )
        return mail.send(msg)
```

### Quick Node.js/SendGrid Example

```javascript
const sgMail = require('@sendgrid/mail');
sgMail.setApiKey(process.env.SENDGRID_API_KEY);

const msg = {
  to: 'user@example.com',
  from: 'noreply@example.com',
  subject: 'Welcome!',
  html: '<h1>Welcome!</h1>'
};

sgMail.send(msg);
```

### Environment Setup

```bash
# .env
MAIL_SERVER=smtp.gmail.com
MAIL_PORT=587
MAIL_USERNAME=your-email@gmail.com
MAIL_PASSWORD=your-app-password
SENDGRID_API_KEY=your-api-key
```

## Level 3: References

### Detailed Implementation Guides
- [Python/Flask SMTP Integration](./references/python-smtp.md) - Complete Flask email service with SMTP
- [Node.js SendGrid Integration](./references/nodejs-sendgrid.md) - SendGrid service with Express routes  
- [Email Templates with MJML](./references/email-templates.md) - MJML template system
- [FastAPI Background Tasks](./references/fastapi-background.md) - Async email processing
- [Email Validation](./references/email-validation.md) - Comprehensive email validation

### Advanced Topics
- Bulk email sending với personalization
- Email queue management với Redis/RabbitMQ
- Email analytics và tracking
- Multi-provider failover systems
- Email templating engines (MJML, Handlebars)

### Testing Examples
```python
# Test email sending
def test_email_service():
    service = EmailService()
    result = service.send_welcome_email("test@example.com", "Test")
    assert result == True
```
