# Authentication Patterns

Comprehensive guide to API authentication methods: JWT, OAuth 2.0, API Keys, and best practices.

## Table of Contents

- [JWT (JSON Web Tokens)](#jwt-json-web-tokens)
- [API Keys](#api-keys)
- [OAuth 2.0 Flows](#oauth-20-flows)
- [Session-Based Auth](#session-based-auth)
- [Best Practices](#best-practices)

---

## JWT (JSON Web Tokens)

### Login Flow

```http
POST /api/v1/auth/login
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "SecurePassword123"
}

Response (200 OK):
{
  "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "expiresIn": 900,
  "tokenType": "Bearer"
}
```

### Using Access Token

```http
GET /api/v1/users/me
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

### Token Refresh

```http
POST /api/v1/auth/refresh
Content-Type: application/json

{
  "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}

Response (200 OK):
{
  "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "expiresIn": 900
}
```

### JWT Structure

```
Header.Payload.Signature

# Header
{
  "alg": "HS256",
  "typ": "JWT"
}

# Payload
{
  "sub": "user_123",
  "email": "user@example.com",
  "role": "admin",
  "iat": 1634400000,
  "exp": 1634400900
}

# Signature
HMACSHA256(
  base64UrlEncode(header) + "." + base64UrlEncode(payload),
  secret
)
```

### JWT Best Practices

1. **Short-lived access tokens** - 15 minutes to 1 hour
2. **Long-lived refresh tokens** - 7-30 days
3. **Store securely** - HttpOnly cookies or secure storage
4. **Include minimal claims** - Don't store sensitive data
5. **Use strong secrets** - 256-bit minimum
6. **Validate on every request** - Check signature and expiration
7. **Implement token rotation** - Issue new refresh token on use

---

## API Keys

### Header-Based (Recommended)

```http
GET /api/v1/data
X-API-Key: sk_live_abc123xyz789
```

### Authorization Header

```http
GET /api/v1/data
Authorization: ApiKey sk_live_abc123xyz789
```

### Query Parameter (Less Secure)

```http
GET /api/v1/public-data?api_key=pk_live_abc123xyz789
```

**⚠️ Use only for public data - logs may expose the key**

### API Key Patterns

**Test vs Production:**
```
sk_test_abc123...  # Test environment
sk_live_xyz789...  # Production environment
```

**Public vs Secret:**
```
pk_live_abc123...  # Public key (safe to expose)
sk_live_xyz789...  # Secret key (never expose)
```

### API Key Best Practices

1. **Prefix keys** - Identify type: `sk_`, `pk_`, `test_`, `live_`
2. **Rate limit per key** - Track usage by API key
3. **Rotation** - Allow users to regenerate keys
4. **Scopes** - Limit what each key can access
5. **Expiration** - Option for time-limited keys
6. **Monitoring** - Track usage patterns and anomalies

---

## OAuth 2.0 Flows

### Authorization Code Flow (Web Apps)

**Step 1: Redirect to Authorization**
```http
GET https://auth.example.com/oauth/authorize?
  response_type=code&
  client_id=abc123&
  redirect_uri=https://myapp.com/callback&
  scope=read:user write:posts&
  state=random_state_string
```

**Step 2: User Grants Permission**
User logs in and approves access

**Step 3: Receive Authorization Code**
```http
GET https://myapp.com/callback?
  code=AUTH_CODE&
  state=random_state_string
```

**Step 4: Exchange Code for Token**
```http
POST https://auth.example.com/oauth/token
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&
code=AUTH_CODE&
client_id=abc123&
client_secret=secret123&
redirect_uri=https://myapp.com/callback

Response (200 OK):
{
  "access_token": "eyJhbGc...",
  "token_type": "Bearer",
  "expires_in": 3600,
  "refresh_token": "eyJhbGc...",
  "scope": "read:user write:posts"
}
```

**Step 5: Use Access Token**
```http
GET /api/v1/users/me
Authorization: Bearer eyJhbGc...
```

### Client Credentials Flow (Server-to-Server)

```http
POST /oauth/token
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&
client_id=abc123&
client_secret=secret123&
scope=api:access

Response (200 OK):
{
  "access_token": "eyJhbGc...",
  "token_type": "Bearer",
  "expires_in": 3600
}
```

### Implicit Flow (Legacy - Not Recommended)

**⚠️ Deprecated due to security concerns. Use Authorization Code Flow with PKCE instead.**

### Password Grant Flow (Legacy - Not Recommended)

**⚠️ Only use for trusted first-party applications.**

```http
POST /oauth/token
Content-Type: application/x-www-form-urlencoded

grant_type=password&
username=user@example.com&
password=SecurePassword123&
client_id=abc123&
client_secret=secret123

Response (200 OK):
{
  "access_token": "eyJhbGc...",
  "token_type": "Bearer",
  "expires_in": 3600,
  "refresh_token": "eyJhbGc..."
}
```

---

## Session-Based Auth

### Login

```http
POST /api/v1/auth/login
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "SecurePassword123"
}

Response (200 OK):
Set-Cookie: session_id=abc123xyz; HttpOnly; Secure; SameSite=Strict; Path=/
{
  "user": {
    "id": "user_123",
    "email": "user@example.com"
  }
}
```

### Authenticated Requests

```http
GET /api/v1/users/me
Cookie: session_id=abc123xyz
```

### Logout

```http
POST /api/v1/auth/logout
Cookie: session_id=abc123xyz

Response (200 OK):
Set-Cookie: session_id=; HttpOnly; Secure; SameSite=Strict; Path=/; Max-Age=0
```

---

## Best Practices

### 1. Always Use HTTPS

Never send credentials over HTTP. Enforce HTTPS at load balancer or reverse proxy level.

### 2. Rate Limit Authentication Endpoints

Prevent brute force attacks:
- `/auth/login`: 5 attempts per 15 minutes per IP
- `/auth/register`: 3 attempts per hour per IP
- `/auth/reset-password`: 3 attempts per hour per email

### 3. Implement Token Blacklisting

For logout and security:
```javascript
// Add to blacklist on logout
await redis.set(`blacklist:${tokenId}`, '1', 'EX', tokenExpirationTime);

// Check on each request
const isBlacklisted = await redis.get(`blacklist:${tokenId}`);
if (isBlacklisted) {
  return res.status(401).json({ error: 'Token revoked' });
}
```

### 4. Use HttpOnly Cookies for Tokens

```javascript
res.cookie('accessToken', token, {
  httpOnly: true,      // Prevents JavaScript access
  secure: true,        // HTTPS only
  sameSite: 'strict',  // CSRF protection
  maxAge: 900000       // 15 minutes
});
```

### 5. Implement CSRF Protection

For cookie-based auth:
```http
POST /api/v1/users
Cookie: session_id=abc123
X-CSRF-Token: csrf_token_abc123
```

### 6. Log Security Events

```javascript
logger.warning('Failed login attempt', {
  email: email,
  ip: req.ip,
  userAgent: req.headers['user-agent']
});

logger.critical('Multiple failed login attempts', {
  email: email,
  ip: req.ip,
  attempts: 5
});
```

### 7. Multi-Factor Authentication (MFA)

```http
POST /api/v1/auth/login
{
  "email": "user@example.com",
  "password": "SecurePassword123"
}

Response (200 OK):
{
  "requiresMFA": true,
  "mfaToken": "temp_token_abc123"
}

# Second request with MFA code
POST /api/v1/auth/mfa-verify
{
  "mfaToken": "temp_token_abc123",
  "code": "123456"
}

Response (200 OK):
{
  "accessToken": "eyJhbGc...",
  "refreshToken": "eyJhbGc..."
}
```

### 8. Password Requirements

- Minimum 8 characters
- At least one uppercase letter
- At least one lowercase letter
- At least one number
- At least one special character
- Check against common password lists
- Hash with bcrypt (cost factor 10-12)

```javascript
const bcrypt = require('bcrypt');

// Hash password
const hashedPassword = await bcrypt.hash(password, 12);

// Verify password
const isValid = await bcrypt.compare(password, hashedPassword);
```

---

## Error Responses

### Invalid Credentials

```http
HTTP/1.1 401 Unauthorized
{
  "error": {
    "code": "INVALID_CREDENTIALS",
    "message": "Email or password is incorrect"
  }
}
```

### Token Expired

```http
HTTP/1.1 401 Unauthorized
{
  "error": {
    "code": "TOKEN_EXPIRED",
    "message": "Access token has expired",
    "details": {
      "expiredAt": "2025-10-16T10:30:00Z"
    }
  }
}
```

### Insufficient Permissions

```http
HTTP/1.1 403 Forbidden
{
  "error": {
    "code": "FORBIDDEN",
    "message": "Insufficient permissions to access this resource",
    "requiredRole": "admin"
  }
}
```

---

## Comparison

| Method | Use Case | Pros | Cons |
|--------|----------|------|------|
| **JWT** | SPAs, Mobile apps | Stateless, Scalable | Can't revoke easily |
| **API Keys** | Server-to-server | Simple, Easy to rotate | Less secure for users |
| **OAuth 2.0** | Third-party access | Secure delegation | Complex setup |
| **Sessions** | Traditional web apps | Easy to revoke | Requires server state |

---

## Security Checklist

- [ ] Always use HTTPS
- [ ] Hash passwords with bcrypt (cost 10-12)
- [ ] Rate limit authentication endpoints
- [ ] Implement token expiration
- [ ] Use HttpOnly cookies for sensitive tokens
- [ ] Implement CSRF protection for cookies
- [ ] Log all authentication events
- [ ] Support MFA for sensitive accounts
- [ ] Validate tokens on every request
- [ ] Never log tokens or passwords
- [ ] Use strong random secrets (256-bit+)
- [ ] Implement account lockout after failed attempts
- [ ] Send security notifications (new login, password change)
- [ ] Allow users to revoke sessions/tokens
