# Email Templates with Mjml

## MJML Template Example

```html
<!-- templates/welcome.mjml -->
<mjml>
  <mj-body>
    <mj-container>
      <mj-section>
        <mj-column>
          <mj-image width="100px" src="https://example.com/logo.png"></mj-image>
        </mj-column>
      </mj-section>

      <mj-section background-color="#f4f4f4">
        <mj-column>
          <mj-text font-size="24px" align="center" color="#333">
            Welcome, {{ userName }}!
          </mj-text>
          <mj-text align="center" color="#666">
            Thank you for joining us. Let's get started!
          </mj-text>
        </mj-column>
      </mj-section>

      <mj-section>
        <mj-column>
          <mj-button href="https://example.com/dashboard" background-color="#007bff">
            Go to Dashboard
          </mj-button>
        </mj-column>
      </mj-section>

      <mj-section>
        <mj-column>
          <mj-text font-size="12px" align="center" color="#999">
            © 2024 Example Inc. All rights reserved.
          </mj-text>
        </mj-column>
      </mj-section>
    </mj-container>
  </mj-body>
</mjml>
```

## Python Template Compilation

```python
# email_templates.py
from mjml import mjml_to_html

def get_welcome_template(user_name):
    with open('templates/welcome.mjml', 'r') as f:
        mjml_content = f.read()

    mjml_content = mjml_content.replace('{{ userName }}', user_name)
    html = mjml_to_html(mjml_content)
    return html

def get_password_reset_template(reset_url):
    with open('templates/reset-password.mjml', 'r') as f:
        mjml_content = f.read()
    
    mjml_content = mjml_content.replace('{{ resetUrl }}', reset_url)
    html = mjml_to_html(mjml_content)
    return html

def get_verification_template(verify_url):
    with open('templates/verify-email.mjml', 'r') as f:
        mjml_content = f.read()
    
    mjml_content = mjml_content.replace('{{ verifyUrl }}', verify_url)
    html = mjml_to_html(mjml_content)
    return html
```

## Node.js Template Engine

```javascript
// email-templates.js
const mjml = require('mjml');
const fs = require('fs');
const path = require('path');

class EmailTemplates {
    static getWelcomeTemplate(userName) {
        const templatePath = path.join(__dirname, '../templates/welcome.mjml');
        let mjmlContent = fs.readFileSync(templatePath, 'utf8');
        
        mjmlContent = mjmlContent.replace(/\{\{userName\}\}/g, userName);
        
        const { html } = mjml(mjmlContent);
        return html;
    }

    static getPasswordResetTemplate(resetUrl) {
        const templatePath = path.join(__dirname, '../templates/reset-password.mjml');
        let mjmlContent = fs.readFileSync(templatePath, 'utf8');
        
        mjmlContent = mjmlContent.replace(/\{\{resetUrl\}\}/g, resetUrl);
        
        const { html } = mjml(mjmlContent);
        return html;
    }

    static getVerificationTemplate(verifyUrl) {
        const templatePath = path.join(__dirname, '../templates/verify-email.mjml');
        let mjmlContent = fs.readFileSync(templatePath, 'utf8');
        
        mjmlContent = mjmlContent.replace(/\{\{verifyUrl\}\}/g, verifyUrl);
        
        const { html } = mjml(mjmlContent);
        return html;
    }
}

module.exports = EmailTemplates;
```

## Template Installation

```bash
# Python
pip install mjml

# Node.js
npm install mjml
```

## Using Templates in Services

### Python Integration

```python
from email_templates import EmailTemplates

class EmailService:
    def send_welcome_email(self, user_email, user_name):
        subject = "Welcome to Our Platform!"
        html_body = EmailTemplates.getWelcomeTemplate(user_name)
        return self.send_email(user_email, subject, html_body=html_body)

    def send_password_reset_email(self, user_email, reset_token):
        subject = "Reset Your Password"
        reset_url = f"https://example.com/reset-password?token={reset_token}"
        html_body = EmailTemplates.getPasswordResetTemplate(reset_url)
        return self.send_email(user_email, subject, html_body=html_body)
```

### Node.js Integration

```javascript
const EmailTemplates = require('./email-templates');

class EmailService {
    async sendWelcomeEmail(to, userName) {
        const htmlContent = EmailTemplates.getWelcomeTemplate(userName);
        return this.sendEmail(to, 'Welcome to Our Platform!', htmlContent);
    }

    async sendPasswordResetEmail(to, resetToken) {
        const resetUrl = `https://example.com/reset-password?token=${resetToken}`;
        const htmlContent = EmailTemplates.getPasswordResetTemplate(resetUrl);
        return this.sendEmail(to, 'Reset Your Password', htmlContent);
    }
}
```

## Dynamic Template Variables

### Python with Jinja2

```python
from jinja2 import Template

def render_template_with_data(template_name, data):
    with open(f'templates/{template_name}.mjml', 'r') as f:
        template = Template(f.read())
    
    mjml_content = template.render(**data)
    html = mjml_to_html(mjml_content)
    return html

# Usage
data = {
    'userName': 'John Doe',
    ' companyName': 'Example Corp',
    'dashboardUrl': 'https://example.com/dashboard',
    'supportEmail': 'support@example.com'
}

html_content = render_template_with_data('welcome', data)
```

### Node.js with Handlebars

```javascript
const Handlebars = require('handlebars');
const mjml = require('mjml');
const fs = require('fs');

function renderTemplate(templateName, data) {
    const templatePath = path.join(__dirname, '../templates', `${templateName}.mjml`);
    let mjmlContent = fs.readFileSync(templatePath, 'utf8');
    
    const template = Handlebars.compile(mjmlContent);
    mjmlContent = template(data);
    
    const { html } = mjml(mjmlContent);
    return html;
}

// Usage
const data = {
    userName: 'John Doe',
    companyName: 'Example Corp',
    dashboardUrl: 'https://example.com/dashboard',
    supportEmail: 'support@example.com'
};

const htmlContent = renderTemplate('welcome', data);
```
