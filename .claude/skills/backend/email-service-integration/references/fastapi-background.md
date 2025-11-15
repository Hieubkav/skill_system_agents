# FastAPI Email with Background Tasks

## Setup

```bash
pip install fastapi fastapi-mail
```

## Email Service with Background Tasks

```python
# email_service.py
from fastapi import BackgroundTasks
from fastapi_mail import FastMail, MessageSchema, ConnectionConfig
import os
from typing import List

class EmailService:
    def __init__(self):
        self.conf = ConnectionConfig(
            mail_server=os.getenv("MAIL_SERVER", "smtp.gmail.com"),
            mail_port=int(os.getenv("MAIL_PORT", 587)),
            mail_from=os.getenv("MAIL_FROM", "noreply@example.com"),
            mail_password=os.getenv("MAIL_PASSWORD"),
            mail_from_name=os.getenv("MAIL_FROM_NAME", "Example App"),
            use_credentials=True,
            validate_certs=True,
            start_tls=True
        )
        self.fm = FastMail(self.conf)

    async def send_email_async(
        self,
        recipients: List[str],
        subject: str,
        body: str,
        subtype: str = "html"
    ):
        """Send email immediately (async)"""
        message = MessageSchema(
            subject=subject,
            recipients=recipients,
            body=body,
            subtype=subtype
        )
        
        await self.fm.send_message(message)

    def send_email_background(
        self,
        background_tasks: BackgroundTasks,
        recipients: List[str],
        subject: str,
        body: str,
        subtype: str = "html"
    ):
        """Queue email as background task"""
        message = MessageSchema(
            subject=subject,
            recipients=recipients,
            body=body,
            subtype=subtype
        )
        
        background_tasks.add_task(self.fm.send_message, message)

    async def send_welcome_email(
        self,
        email: str,
        name: str,
        background_tasks: BackgroundTasks = None
    ):
        """Send welcome email"""
        html_body = f"""
        <h1>Welcome, {name}!</h1>
        <p>Thank you for joining us. Let's get started!</p>
        <a href="https://example.com/dashboard">Start Exploring</a>
        """
        
        subject = "Welcome to Our Platform!"
        
        if background_tasks:
            self.send_email_background(
                background_tasks=background_tasks,
                recipients=[email],
                subject=subject,
                body=html_body
            )
        else:
            await self.send_email_async(
                recipients=[email],
                subject=subject,
                body=html_body
            )

    async def send_verification_email(
        self,
        email: str,
        verification_token: str,
        background_tasks: BackgroundTasks = None
    ):
        """Send email verification"""
        verify_url = f"https://example.com/verify-email?token={verification_token}"
        html_body = f"""
        <h1>Verify Your Email Address</h1>
        <p>Click the link below to verify your email:</p>
        <a href="{verify_url}">Verify Email</a>
        <p>This link expires in 24 hours.</p>
        """
        
        subject = "Verify Your Email"
        
        if background_tasks:
            self.send_email_background(
                background_tasks=background_tasks,
                recipients=[email],
                subject=subject,
                body=html_body
            )
        else:
            await self.send_email_async(
                recipients=[email],
                subject=subject,
                body=html_body
            )

    async def send_password_reset_email(
        self,
        email: str,
        reset_token: str,
        background_tasks: BackgroundTasks = None
    ):
        """Send password reset email"""
        reset_url = f"https://example.com/reset-password?token={reset_token}"
        html_body = f"""
        <h1>Reset Your Password</h1>
        <p>Click the link below to reset your password:</p>
        <a href="{reset_url}">Reset Password</a>
        <p>This link expires in 24 hours.</p>
        """
        
        subject = "Reset Your Password"
        
        if background_tasks:
            self.send_email_background(
                background_tasks=background_tasks,
                recipients=[email],
                subject=subject,
                body=html_body
            )
        else:
            await self.send_email_async(
                recipients=[email],
                subject=subject,
                body=html_body
            )

    async def send_notification_email(
        self,
        email: str,
        notification_data: dict,
        background_tasks: BackgroundTasks = None
    ):
        """Send notification email"""
        title = notification_data.get('title', 'Notification')
        message = notification_data.get('message', '')
        html_content = notification_data.get('html_content', '')
        
        html_body = f"""
        <h1>{title}</h1>
        <p>{message}</p>
        {html_content}
        """
        
        subject = notification_data.get('subject', title)
        
        if background_tasks:
            self.send_email_background(
                background_tasks=background_tasks,
                recipients=[email],
                subject=subject,
                body=html_body
            )
        else:
            await self.send_email_async(
                recipients=[email],
                subject=subject,
                body=html_body
            )

    async def send_bulk_emails(
        self,
        recipients: List[dict],
        subject: str,
        html_body: str,
        background_tasks: BackgroundTasks = None
    ):
        """Send bulk emails with personalization"""
        for recipient in recipients:
            personalized_body = html_body.replace("{{name}}", recipient.get('name', 'User'))
            personalized_body = personalized_body.replace("{{email}}", recipient.get('email', ''))
            
            if background_tasks:
                self.send_email_background(
                    background_tasks=background_tasks,
                    recipients=[recipient.get('email')],
                    subject=subject,
                    body=personalized_body
                )
            else:
                await self.send_email_async(
                    recipients=[recipient.get('email')],
                    subject=subject,
                    body=personalized_body
                )

# Singleton instance
email_service = EmailService()
```

## FastAPI Routes

```python
# routes/email.py
from fastapi import APIRouter, BackgroundTasks, HTTPException
from pydantic import BaseModel, EmailStr
from typing import List
from email_service import email_service

router = APIRouter(prefix="/api/email", tags=["email"])

class EmailRequest(BaseModel):
    recipients: List[EmailStr]
    subject: str
    body: str

class WelcomeEmailRequest(BaseModel):
    email: EmailStr
    name: str
    background: bool = True

class VerificationEmailRequest(BaseModel):
    email: EmailStr
    background: bool = True

class PasswordResetRequest(BaseModel):
    email: EmailStr
    background: bool = True

@router.post("/send")
async def send_email(
    email_request: EmailRequest,
    background_tasks: BackgroundTasks
):
    """Send email immediately or as background task"""
    try:
        email_service.send_email_background(
            background_tasks=background_tasks,
            recipients=email_request.recipients,
            subject=email_request.subject,
            body=email_request.body
        )
        
        return {"message": "Email queued for sending"}
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))

@router.post("/welcome")
async def send_welcome_email(
    request: WelcomeEmailRequest,
    background_tasks: BackgroundTasks
):
    """Send welcome email"""
    try:
        if request.background:
            await email_service.send_welcome_email(
                email=request.email,
                name=request.name,
                background_tasks=background_tasks
            )
        else:
            await email_service.send_welcome_email(
                email=request.email,
                name=request.name
            )
        
        return {"message": "Welcome email queued for sending"}
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))

@router.post("/verify")
async def send_verification_email(
    request: VerificationEmailRequest,
    background_tasks: BackgroundTasks
):
    """Send email verification"""
    try:
        verification_token = generate_verification_token()
        
        if request.background:
            await email_service.send_verification_email(
                email=request.email,
                verification_token=verification_token,
                background_tasks=background_tasks
            )
        else:
            await email_service.send_verification_email(
                email=request.email,
                verification_token=verification_token
            )
        
        # Store token in database
        await VerificationToken.create(email=request.email, token=verification_token)
        
        return {"message": "Verification email sent"}
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))

@router.post("/reset-password")
async def send_password_reset_email(
    request: PasswordResetRequest,
    background_tasks: BackgroundTasks
):
    """Send password reset email"""
    try:
        user = await User.get_or_none(email=request.email)
        
        # Don't reveal if email exists
        if not user:
            return {"message": "If email exists, reset link sent"}
        
        reset_token = generate_reset_token()
        
        if request.background:
            await email_service.send_password_reset_email(
                email=request.email,
                reset_token=reset_token,
                background_tasks=background_tasks
            )
        else:
            await email_service.send_password_reset_email(
                email=request.email,
                reset_token=reset_token
            )
        
        # Store token in database
        await ResetToken.create(user_id=user.id, token=reset_token)
        
        return {"message": "Password reset email sent"}
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))

@router.post("/bulk")
async def send_bulk_emails(
    recipients: List[dict],
    subject: str,
    html_body: str,
    background_tasks: BackgroundTasks
):
    """Send bulk emails with personalization"""
    try:
        await email_service.send_bulk_emails(
            recipients=recipients,
            subject=subject,
            html_body=html_body,
            background_tasks=background_tasks
        )
        
        return {"message": f"Bulk emails queued for {len(recipients)} recipients"}
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))
```

## Main App Integration

```python
# main.py
from fastapi import FastAPI
from routes.email import router as email_router

app = FastAPI(title="Email Service API")

# Include email routes
app.include_router(email_router)

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8000)
```

## Environment Variables

```bash
# .env
MAIL_SERVER=smtp.gmail.com
MAIL_PORT=587
MAIL_FROM=noreply@example.com
MAIL_PASSWORD=your-app-password
MAIL_FROM_NAME="Example App"
```

## Testing With Background Tasks

```python
# test_email.py
import asyncio
from fastapi import BackgroundTasks
from email_service import EmailService

async def test_email_service():
    background_tasks = BackgroundTasks()
    email_service = EmailService()
    
    # Test background email
    await email_service.send_welcome_email(
        email="test@example.com",
        name="Test User",
        background_tasks=background_tasks
    )
    
    # The email will be sent after the response is returned
    print("Email queued successfully")

if __name__ == "__main__":
    asyncio.run(test_email_service())
```
