# Secure Session Management

## Session ID Requirements

### Properties

| Property | Requirement | Why |
|----------|-------------|-----|
| Length | 128 bits (32 base64 chars) | Resistant to brute force |
| Randomness | Cryptographically secure | Prevent guessing attacks |
| Entropy | High entropy | Unique across all sessions |
| Obscurity | No meaningful content | Hide session structure |
| Uniqueness | Never duplicate | Prevent session fixation |

### Generation (Node.js)

```typescript
import { randomBytes } from 'crypto';

function generateSessionId(): string {
    return randomBytes(32).toString('hex');  // 64 chars, 256 bits
}
```

## Cookie Security Attributes

### HttpOnly
- ❌ JavaScript cannot access cookie
- ✓ Prevents XSS attacks from stealing session ID
- ⚠️ CSRF attacks still work (browser auto-includes cookie)

### Secure
- ✓ Cookie only sent over HTTPS
- ✓ Prevents man-in-the-middle interception
- Must use on production

### SameSite
```typescript
// Strict (safest)
Set-Cookie: sessionId=abc; SameSite=Strict
// Cookie only sent to same site, prevents CSRF

// Lax (balanced)
Set-Cookie: sessionId=abc; SameSite=Lax
// Sent on cross-site top-level navigations (links)

// None (requires Secure)
Set-Cookie: sessionId=abc; SameSite=None; Secure
// Sent in all contexts (cross-site)
```

### Max-Age / Expires
- Session cookies: no expiry (deleted on browser close)
- Persistent cookies: set reasonable timeout (24 hours typical)

## Complete Secure Cookie

```typescript
Set-Cookie: __Host-sessionId=random_value_here;
            HttpOnly;
            Secure;
            SameSite=Strict;
            Path=/;
            Max-Age=86400;
            Domain=myapp.com
```

## Session Storage

### Server-Side Storage Options

**Redis** (recommended)
```typescript
const sessionStore = new RedisStore({
    client: redisClient,
    ttl: 24 * 60 * 60  // 24 hours
});
```

**Database** (PostgreSQL)
```sql
CREATE TABLE sessions (
    id VARCHAR(64) PRIMARY KEY,
    user_id INT NOT NULL,
    data JSONB,
    expires_at TIMESTAMP,
    created_at TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id)
);

CREATE INDEX idx_sessions_expires ON sessions(expires_at);
```

## Session Lifecycle

### 1. Login
```typescript
app.post('/login', async (req, res) => {
    const user = await authenticate(email, password);
    
    // Create NEW session (prevent fixation)
    req.session.regenerate((err) => {
        if (err) return res.status(500).end();
        
        req.session.userId = user.id;
        req.session.role = user.role;
        res.json({ user });
    });
});
```

### 2. On Every Request
- Validate session exists in store
- Check expiration
- Refresh expiry if valid

### 3. Logout
```typescript
app.post('/logout', (req, res) => {
    req.session.destroy((err) => {
        res.clearCookie('connect.sid');
        res.json({ message: 'Logged out' });
    });
});
```

## Common Threats & Mitigations

| Threat | Attack | Defense |
|--------|--------|---------|
| XSS | Steal session cookie | HttpOnly flag |
| CSRF | Forge request using cookie | SameSite=Strict, CSRF token |
| Session Fixation | Victim uses attacker's session | Regenerate on login |
| Session Hijacking | Steal session ID from network | HTTPS + Secure flag |
| Replay Attack | Reuse old session | Short expiry + regeneration |

## CSRF Protection (When Using Sessions)

### Token-Based
```html
<form method="POST" action="/transfer">
    <input type="hidden" name="_csrf" value="{{csrfToken}}">
    <input type="text" name="amount">
    <button>Transfer</button>
</form>
```

Server validation:
```typescript
app.use(csrf());  // Middleware validates token

app.post('/transfer', (req, res) => {
    // CSRF token already validated by middleware
    const { amount } = req.body;
    // Process transfer
});
```

### Double-Submit Cookie
```typescript
// Set CSRF token in both cookie AND request body/header
// Server verifies they match
if (req.headers['x-csrf-token'] !== req.cookies.csrfToken) {
    return res.status(403).json({ error: 'CSRF validation failed' });
}
```

