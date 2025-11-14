# Error Handling & Validation

Comprehensive guide to API error handling, validation patterns, and error response structures.

## Table of Contents

- [Error Response Structure](#error-response-structure)
- [Validation Errors](#validation-errors)
- [Business Logic Errors](#business-logic-errors)
- [Rate Limiting Errors](#rate-limiting-errors)
- [Input Validation](#input-validation)
- [Best Practices](#best-practices)

---

## Error Response Structure

### Standard Error Format

```json
{
  "error": {
    "code": "ERROR_CODE",
    "message": "Human-readable error message",
    "field": "fieldName",
    "details": {
      "additionalInfo": "value"
    }
  }
}
```

### Single Error Example

```http
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
  "error": {
    "code": "INVALID_EMAIL",
    "message": "Email address is invalid",
    "field": "email",
    "details": "Email must contain @ symbol and valid domain"
  }
}
```

---

## Validation Errors

### Multiple Field Errors

```http
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Request validation failed",
    "errors": [
      {
        "field": "email",
        "message": "Email is required",
        "code": "REQUIRED_FIELD"
      },
      {
        "field": "password",
        "message": "Password must be at least 8 characters",
        "code": "INVALID_LENGTH"
      },
      {
        "field": "age",
        "message": "Age must be at least 18",
        "code": "INVALID_VALUE",
        "constraints": {
          "min": 18,
          "received": 15
        }
      }
    ]
  }
}
```

### Common Validation Error Codes

- `REQUIRED_FIELD` - Field is required but missing
- `INVALID_FORMAT` - Format is incorrect (email, phone, etc.)
- `INVALID_TYPE` - Wrong data type
- `INVALID_LENGTH` - String too short or too long
- `INVALID_RANGE` - Number out of range
- `INVALID_VALUE` - Value not in allowed set
- `INVALID_PATTERN` - Doesn't match regex pattern
- `DUPLICATE_VALUE` - Value must be unique

---

## Business Logic Errors

### Insufficient Funds Example

```http
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
  "error": {
    "code": "INSUFFICIENT_FUNDS",
    "message": "Account balance too low for this transaction",
    "details": {
      "balance": 50.00,
      "required": 100.00,
      "currency": "USD"
    }
  }
}
```

### Resource Conflict Example

```http
HTTP/1.1 409 Conflict
Content-Type: application/json

{
  "error": {
    "code": "EMAIL_ALREADY_EXISTS",
    "message": "An account with this email already exists",
    "field": "email",
    "details": {
      "email": "user@example.com"
    }
  }
}
```

### Resource Not Found Example

```http
HTTP/1.1 404 Not Found
Content-Type: application/json

{
  "error": {
    "code": "USER_NOT_FOUND",
    "message": "User with ID 123 does not exist",
    "details": {
      "userId": "123"
    }
  }
}
```

### Permission Denied Example

```http
HTTP/1.1 403 Forbidden
Content-Type: application/json

{
  "error": {
    "code": "PERMISSION_DENIED",
    "message": "You don't have permission to delete this resource",
    "details": {
      "requiredPermission": "delete:users",
      "userPermissions": ["read:users", "write:users"]
    }
  }
}
```

---

## Rate Limiting Errors

```http
HTTP/1.1 429 Too Many Requests
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 0
X-RateLimit-Reset: 1634400000
Retry-After: 3600
Content-Type: application/json

{
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "API rate limit exceeded",
    "details": {
      "limit": 1000,
      "window": "1 hour",
      "retryAfter": 3600,
      "resetAt": "2025-10-16T11:00:00Z"
    }
  }
}
```

---

## Input Validation

### Validation with Pydantic (Python)

```python
from pydantic import BaseModel, EmailStr, Field, validator
from typing import Optional

class UserCreate(BaseModel):
    email: EmailStr
    password: str = Field(min_length=8, max_length=100)
    name: str = Field(min_length=1, max_length=100)
    age: int = Field(ge=18, le=120)
    phone: Optional[str] = None

    @validator('password')
    def password_strength(cls, v):
        if not any(c.isupper() for c in v):
            raise ValueError('Password must contain uppercase letter')
        if not any(c.isdigit() for c in v):
            raise ValueError('Password must contain digit')
        if not any(c in '!@#$%^&*' for c in v):
            raise ValueError('Password must contain special character')
        return v

    @validator('phone')
    def phone_format(cls, v):
        if v and not v.startswith('+'):
            raise ValueError('Phone must start with country code (+)')
        return v
```

### Validation with Joi (Node.js)

```javascript
const Joi = require('joi');

const userSchema = Joi.object({
  email: Joi.string()
    .email()
    .required()
    .messages({
      'string.email': 'Email must be valid',
      'any.required': 'Email is required'
    }),
  
  password: Joi.string()
    .min(8)
    .max(100)
    .pattern(/^(?=.*[A-Z])(?=.*\d)(?=.*[!@#$%^&*])/)
    .required()
    .messages({
      'string.min': 'Password must be at least 8 characters',
      'string.pattern.base': 'Password must contain uppercase, digit, and special character'
    }),
  
  name: Joi.string()
    .min(1)
    .max(100)
    .required(),
  
  age: Joi.number()
    .integer()
    .min(18)
    .max(120)
    .required(),
  
  phone: Joi.string()
    .pattern(/^\+[1-9]\d{1,14}$/)
    .optional()
});

// Validate
const { error, value } = userSchema.validate(req.body, { 
  abortEarly: false 
});

if (error) {
  return res.status(400).json({
    error: {
      code: 'VALIDATION_ERROR',
      message: 'Request validation failed',
      errors: error.details.map(err => ({
        field: err.path.join('.'),
        message: err.message,
        code: err.type.toUpperCase()
      }))
    }
  });
}
```

### Validation with Laravel (PHP)

```php
use Illuminate\Http\Request;
use Illuminate\Validation\Rules\Password;

$validated = $request->validate([
    'email' => ['required', 'email', 'unique:users'],
    'password' => ['required', Password::min(8)
        ->mixedCase()
        ->numbers()
        ->symbols()],
    'name' => ['required', 'string', 'min:1', 'max:100'],
    'age' => ['required', 'integer', 'min:18', 'max:120'],
    'phone' => ['nullable', 'regex:/^\+[1-9]\d{1,14}$/']
], [
    'email.required' => 'Email is required',
    'email.email' => 'Email must be valid',
    'email.unique' => 'Email already exists',
    'password.min' => 'Password must be at least 8 characters'
]);
```

---

## Best Practices

### 1. Use Appropriate Status Codes

- `400 Bad Request` - Validation errors, malformed requests
- `401 Unauthorized` - Missing or invalid authentication
- `403 Forbidden` - Authenticated but not authorized
- `404 Not Found` - Resource doesn't exist
- `409 Conflict` - Resource conflict (duplicate email, version mismatch)
- `422 Unprocessable Entity` - Valid syntax but semantic errors
- `429 Too Many Requests` - Rate limit exceeded
- `500 Internal Server Error` - Unexpected server errors

### 2. Consistent Error Structure

Always use the same error format across your API:

```json
{
  "error": {
    "code": "ERROR_CODE",
    "message": "Human-readable message",
    "field": "fieldName",
    "details": {}
  }
}
```

### 3. Don't Leak Sensitive Information

**❌ Bad:**
```json
{
  "error": {
    "message": "SQL Error: Duplicate entry 'user@example.com' for key 'users.email'",
    "stack": "at Connection.query (/app/db.js:123)"
  }
}
```

**✅ Good:**
```json
{
  "error": {
    "code": "EMAIL_ALREADY_EXISTS",
    "message": "An account with this email already exists",
    "field": "email"
  }
}
```

### 4. Provide Actionable Error Messages

**❌ Bad:**
```json
{
  "error": {
    "message": "Invalid input"
  }
}
```

**✅ Good:**
```json
{
  "error": {
    "code": "INVALID_EMAIL",
    "message": "Email address is invalid",
    "field": "email",
    "details": "Email must be in format: user@domain.com"
  }
}
```

### 5. Validate Early

Validate input as early as possible in the request lifecycle:

```javascript
// Express.js middleware
const validateUser = (req, res, next) => {
  const { error } = userSchema.validate(req.body);
  if (error) {
    return res.status(400).json({
      error: {
        code: 'VALIDATION_ERROR',
        message: 'Request validation failed',
        errors: formatJoiErrors(error)
      }
    });
  }
  next();
};

app.post('/api/users', validateUser, createUser);
```

### 6. Sanitize Outputs

Always escape user input when returning in errors:

```javascript
const escapeHtml = require('escape-html');

const message = escapeHtml(userInput);
```

### 7. Log Errors Appropriately

```javascript
// Log for monitoring
logger.error('Database connection failed', {
  error: err.message,
  stack: err.stack,
  userId: req.user?.id,
  endpoint: req.path
});

// Return safe error to client
res.status(500).json({
  error: {
    code: 'INTERNAL_SERVER_ERROR',
    message: 'An unexpected error occurred'
  }
});
```

### 8. Use Error Codes for Client Logic

Clients can use error codes for conditional logic:

```javascript
// Client-side handling
try {
  await api.createUser(userData);
} catch (error) {
  if (error.code === 'EMAIL_ALREADY_EXISTS') {
    // Show "Email taken" UI
  } else if (error.code === 'VALIDATION_ERROR') {
    // Highlight invalid fields
  } else {
    // Generic error UI
  }
}
```

---

## Common Error Codes Reference

### Authentication & Authorization
- `INVALID_CREDENTIALS` - Wrong email/password
- `TOKEN_EXPIRED` - Access token expired
- `TOKEN_INVALID` - Malformed or invalid token
- `PERMISSION_DENIED` - Insufficient permissions
- `ACCOUNT_DISABLED` - Account suspended or disabled

### Validation
- `VALIDATION_ERROR` - General validation failure
- `REQUIRED_FIELD` - Missing required field
- `INVALID_FORMAT` - Wrong format
- `INVALID_TYPE` - Wrong data type
- `INVALID_LENGTH` - String length constraint
- `INVALID_RANGE` - Number range constraint
- `INVALID_VALUE` - Value not allowed

### Resources
- `RESOURCE_NOT_FOUND` - Resource doesn't exist
- `RESOURCE_ALREADY_EXISTS` - Duplicate resource
- `RESOURCE_CONFLICT` - State conflict

### Business Logic
- `INSUFFICIENT_FUNDS` - Not enough balance
- `QUOTA_EXCEEDED` - Usage limit reached
- `OPERATION_NOT_ALLOWED` - Business rule violation

### System
- `INTERNAL_SERVER_ERROR` - Unexpected error
- `SERVICE_UNAVAILABLE` - System down/maintenance
- `RATE_LIMIT_EXCEEDED` - Too many requests
- `TIMEOUT` - Request timeout

---

## GraphQL Error Handling

### Error Format

```json
{
  "data": null,
  "errors": [
    {
      "message": "Email already exists",
      "extensions": {
        "code": "EMAIL_ALREADY_EXISTS",
        "field": "email"
      }
    }
  ]
}
```

### Structured Errors in Mutations

```graphql
type UserPayload {
  user: User
  errors: [UserError!]
}

type UserError {
  field: String!
  message: String!
  code: String!
}

type Mutation {
  createUser(input: CreateUserInput!): UserPayload!
}
```

---

## Testing Error Handling

```javascript
// Test validation error
test('should return 400 for invalid email', async () => {
  const res = await request(app)
    .post('/api/users')
    .send({ email: 'invalid', password: 'Test123!' });
  
  expect(res.status).toBe(400);
  expect(res.body.error.code).toBe('VALIDATION_ERROR');
  expect(res.body.error.errors).toContainEqual(
    expect.objectContaining({
      field: 'email',
      code: 'INVALID_FORMAT'
    })
  );
});

// Test duplicate resource
test('should return 409 for duplicate email', async () => {
  await createUser({ email: 'test@example.com' });
  
  const res = await request(app)
    .post('/api/users')
    .send({ email: 'test@example.com', password: 'Test123!' });
  
  expect(res.status).toBe(409);
  expect(res.body.error.code).toBe('EMAIL_ALREADY_EXISTS');
});
```
