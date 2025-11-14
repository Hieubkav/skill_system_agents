---
name: auth-implementation-patterns
description: Master authentication and authorization patterns including JWT, OAuth2, session management, and RBAC to build secure, scalable access control systems. Use when implementing auth systems, securing APIs, or debugging security issues.
---

# Authentication & Authorization Implementation Patterns

Build secure, scalable authentication and authorization systems using industry-standard patterns and modern best practices.

## Level 1: Overview

### When to Use This Skill

- Implementing user authentication systems
- Securing REST or GraphQL APIs
- Adding OAuth2/social login
- Implementing role-based access control (RBAC)
- Designing session management
- Debugging auth issues

### Core Concepts

**Authentication (AuthN)**: Who are you?
- Verifying identity (username/password, OAuth, biometrics)
- Issuing credentials (sessions, tokens)

**Authorization (AuthZ)**: What can you do?
- Permission checking
- Role-based access control (RBAC)
- Resource ownership validation

### Key Patterns

1. **JWT**: Stateless, self-contained tokens (15-30min expiry)
2. **Sessions**: Stateful, server-side session storage (HttpOnly cookies)
3. **OAuth2**: Delegated authentication (social login)
4. **RBAC**: Role → Permission → User model

---

## Level 2: Quick Start

### JWT Pattern: Access + Refresh Tokens

```typescript
// Generate tokens
const accessToken = jwt.sign(payload, SECRET, { expiresIn: '15m' });
const refreshToken = jwt.sign({ userId }, REFRESH_SECRET, { expiresIn: '7d' });

// Middleware
function authenticate(req, res, next) {
    const token = req.headers.authorization?.substring(7);
    try {
        const payload = jwt.verify(token, SECRET);
        req.user = payload;
        next();
    } catch {
        res.status(401).json({ error: 'Invalid token' });
    }
}
```

### Session Pattern: Express + Redis

```typescript
app.use(session({
    store: new RedisStore(),
    secret: process.env.SESSION_SECRET,
    cookie: { secure: true, httpOnly: true, sameSite: 'strict' }
}));

app.post('/login', async (req, res) => {
    const user = await db.users.findOne({ email });
    if (await verifyPassword(password, user.passwordHash)) {
        req.session.userId = user.id;
        res.json({ user });
    }
});
```

### RBAC: Role → Permissions

```typescript
const rolePermissions = {
    admin: ['read:users', 'write:users', 'delete:users'],
    user: ['read:posts', 'write:posts']
};

function requirePermission(...perms) {
    return (req, res, next) => {
        const hasAll = perms.every(p => 
            rolePermissions[req.user.role]?.includes(p)
        );
        if (!hasAll) return res.status(403).json({ error: 'Forbidden' });
        next();
    };
}
```

### Security Checklist

✓ Use HTTPS everywhere
✓ Short-lived access tokens (15m)
✓ Secure cookies: HttpOnly, Secure, SameSite flags
✓ Hash passwords with bcrypt/argon2 (12+ rounds)
✓ Rate limit auth endpoints (5 attempts/15min)
✓ Validate all input server-side

---

## Level 3: References

- **references/jwt-best-practices.md** - JWT token generation, validation, refresh flow
- **references/oauth2-flows.md** - Authorization code, client credentials, PKCE
- **references/session-security.md** - Cookie security, session ID generation, CSRF protection
- **references/rbac-patterns.md** - Role hierarchy, permission mapping, resource ownership
- **assets/auth-security-checklist.md** - Production readiness checklist
- **assets/password-policy-template.md** - Password requirements and validation
- **scripts/token-validator.ts** - JWT validation utility

