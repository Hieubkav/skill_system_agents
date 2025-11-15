# Node.js with SendGrid Integration

## Setup

```bash
npm install @sendgrid/mail
```

## Email Service Implementation

```javascript
// email-service.js
const sgMail = require('@sendgrid/mail');
const logger = require('./logger');

sgMail.setApiKey(process.env.SENDGRID_API_KEY);

class EmailService {
    async sendEmail(to, subject, htmlContent, textContent = null) {
        try {
            const msg = {
                to: Array.isArray(to) ? to : [to],
                from: process.env.MAIL_FROM || 'noreply@example.com',
                subject: subject,
                html: htmlContent,
                ...(textContent && { text: textContent })
            };

            const result = await sgMail.send(msg);
            logger.info(`Email sent to ${to}: ${subject}`);
            return { success: true, messageId: result[0].headers['x-message-id'] };
        } catch (error) {
            logger.error(`Failed to send email: ${error.message}`);
            return { success: false, error: error.message };
        }
    }

    async sendWelcomeEmail(to, userName) {
        const htmlContent = `
            <h1>Welcome, ${userName}!</h1>
            <p>Thank you for joining us.</p>
            <a href="https://example.com/dashboard">Start Exploring</a>
        `;

        return this.sendEmail(to, 'Welcome to Our Platform!', htmlContent);
    }

    async sendPasswordResetEmail(to, resetToken) {
        const resetUrl = `https://example.com/reset-password?token=${resetToken}`;
        const htmlContent = `
            <h1>Reset Your Password</h1>
            <p>Click the link below to reset your password:</p>
            <a href="${resetUrl}">Reset Password</a>
            <p>This link expires in 24 hours.</p>
        `;

        return this.sendEmail(to, 'Reset Your Password', htmlContent);
    }

    async sendVerificationEmail(to, verificationToken) {
        const verifyUrl = `https://example.com/verify-email?token=${verificationToken}`;
        const htmlContent = `
            <h1>Verify Your Email</h1>
            <p>Click the link below to verify your email:</p>
            <a href="${verifyUrl}">Verify Email</a>
        `;

        return this.sendEmail(to, 'Verify Your Email', htmlContent);
    }

    async sendBulkEmails(recipients, subject, htmlContent) {
        const results = [];
        for (const recipient of recipients) {
            const result = await this.sendEmail(recipient, subject, htmlContent);
            results.push({ email: recipient, ...result });
        }
        return results;
    }

    async sendTemplateEmail(to, templateId, dynamicData) {
        try {
            const msg = {
                to: Array.isArray(to) ? to : [to],
                from: process.env.MAIL_FROM || 'noreply@example.com',
                templateId: templateId,
                dynamicTemplateData: dynamicData
            };

            const result = await sgMail.send(msg);
            logger.info(`Template email sent to ${to}: ${templateId}`);
            return { success: true, messageId: result[0].headers['x-message-id'] };
        } catch (error) {
            logger.error(`Failed to send template email: ${error.message}`);
            return { success: false, error: error.message };
        }
    }
}

module.exports = EmailService;
```

## Express Routes

```javascript
// routes/email.js
const express = require('express');
const router = express.Router();
const EmailService = require('../services/email-service');

const emailService = new EmailService();

router.post('/send-verification', async (req, res) => {
    try {
        const { email } = req.body;
        const verificationToken = generateToken();
        
        const result = await emailService.sendVerificationEmail(email, verificationToken);
        
        if (result.success) {
            // Store token in database
            await VerificationToken.create({ email, token: verificationToken });
            return res.status(200).json({ message: 'Verification email sent' });
        } else {
            return res.status(500).json({ error: 'Failed to send email' });
        }
    } catch (error) {
        return res.status(500).json({ error: error.message });
    }
});

router.post('/send-reset', async (req, res) => {
    try {
        const { email } = req.body;
        const user = await User.findOne({ email });

        // Don't reveal if email exists
        if (!user) {
            return res.status(200).json({ message: 'If email exists, reset link sent' });
        }

        const resetToken = generateToken();
        const result = await emailService.sendPasswordResetEmail(email, resetToken);

        if (result.success) {
            await ResetToken.create({ userId: user._id, token: resetToken });
            return res.status(200).json({ message: 'Reset email sent' });
        } else {
            return res.status(500).json({ error: 'Failed to send email' });
        }
    } catch (error) {
        return res.status(500).json({ error: error.message });
    }
});

router.post('/bulk-send', async (req, res) => {
    try {
        const { recipients, subject, htmlContent } = req.body;
        const results = await emailService.sendBulkEmails(recipients, subject, htmlContent);
        
        const successful = results.filter(r => r.success).length;
        const failed = results.filter(r => !r.success).length;
        
        return res.status(200).json({
            message: `Sent ${successful} emails, ${failed} failed`,
            results
        });
    } catch (error) {
        return res.status(500).json({ error: error.message });
    }
});

module.exports = router;
```

## Environment Variables

```bash
# .env
SENDGRID_API_KEY=your-sendgrid-api-key
MAIL_FROM=noreply@example.com
```

## Install Dependencies

```bash
npm install express dotenv
```
