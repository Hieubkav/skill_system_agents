# Authentication & Authorization Security Checklist

## Pre-Launch Checklist

### 1. Transport Security
- [ ] All auth endpoints use HTTPS (no HTTP exceptions)
- [ ] HSTS header enabled: `Strict-Transport-Security: max-age=31536000; includeSubDomains`
- [ ] Certificate pinning enabled (for mobile apps)
- [ ] TLS 1.2+ enforced

### 2. JWT Implementation
- [ ] Access tokens: 15-30 minute expiration
- [ ] Refresh tokens: 7 day expiration, stored in HttpOnly cookies
- [ ] Asymmetric algorithms (RS256 or EdDSA) for distributed systems
- [ ] No PII in JWT payload
- [ ] JTI (JWT ID) claim included for revocation tracking
- [ ] Issuer (iss) and audience (aud) validated on every request
- [ ] Algorithm explicitly validated (prevent "none" or algorithm confusion)

### 3. Session Management
- [ ] Session ID: 128+ bits, cryptographically random
- [ ] Cookie flags: HttpOnly, Secure, SameSite=Strict
- [ ] Session timeout: 24 hours (or shorter for sensitive apps)
- [ ] Session regeneration on login
- [ ] Session invalidation on logout
- [ ] Server-side session storage (Redis/Database, not client-side)

### 4. Password Security
- [ ] Minimum 8 characters (recommend 12+)
- [ ] Mixed case, numbers, special characters required
- [ ] Password hashing: bcrypt (12+ rounds) or Argon2
- [ ] Never log or store plaintext passwords
- [ ] Password strength meter on frontend (UX, not validation)
- [ ] Password history: prevent reuse of last 5 passwords
- [ ] Expiration policy: 90 days or risk-based

### 5. Rate Limiting
- [ ] Login endpoint: 5 attempts per 15 minutes
- [ ] Password reset: 3 requests per hour
- [ ] API endpoints: 100 requests per minute (varies by endpoint)
- [ ] Registration: 3 accounts per IP per day
- [ ] Use distributed rate limiting (Redis) for multi-server setups

### 6. Input Validation
- [ ] All inputs validated server-side (never trust client)
- [ ] Email validation: RFC 5321 compliant
- [ ] Username: alphanumeric + underscore, 3-32 characters
- [ ] Password field: masked in UI, never logged
- [ ] SQL injection protection: parameterized queries
- [ ] XSS protection: sanitize output, use templating engines

### 7. OAuth2 / Social Login
- [ ] State parameter validated (CSRF protection)
- [ ] PKCE enabled for SPAs and mobile apps
- [ ] Redirect URI whitelist (exact match, no wildcards)
- [ ] Scope minimization: request only needed permissions
- [ ] Access token: 1 hour max lifetime
- [ ] Refresh token rotation on use
- [ ] Email verification required for new accounts

### 8. Multi-Factor Authentication (if required)
- [ ] 2FA not optional for admin accounts
- [ ] TOTP (Google Authenticator) supported
- [ ] Backup codes: 10x 8-character codes
- [ ] Email OTP: 6-digit code, 5 minute expiry
- [ ] SMS OTP: 6-digit code, 2 minute expiry (if using)
- [ ] Rate limit: 3 OTP attempts per minute

### 9. Account Recovery
- [ ] Password reset: email-based, 30 minute expiry
- [ ] Password reset token: cryptographically random, one-time use
- [ ] Account lockout: 15 minutes after 5 failed attempts
- [ ] Lockout notification: email alert
- [ ] Security questions: optional (weak, don't rely solely)
- [ ] Account recovery via verified email only

### 10. API Authorization
- [ ] RBAC (Role-Based) or ABAC (Attribute-Based) implemented
- [ ] Least privilege principle: minimal permissions
- [ ] Resource ownership validated (can't access others' data)
- [ ] Audit logging: all auth/authz decisions
- [ ] Permission checks: both frontend (UX) and backend (security)
- [ ] API keys: if used, rotate every 90 days

### 11. Data Protection
- [ ] Encryption at rest: AES-256 for sensitive data
- [ ] Encryption in transit: TLS 1.2+
- [ ] PII fields: never log or expose in error messages
- [ ] Audit logs: immutable, queryable by user/action
- [ ] Database: separate read replicas for sensitive operations
- [ ] Secrets: stored in environment variables or secret manager

### 12. Error Handling
- [ ] Generic error messages: "Invalid email or password" (don't reveal user exists)
- [ ] No stack traces exposed to clients
- [ ] No system paths, API details, or software versions leaked
- [ ] CORS headers: whitelist specific origins, never `*`
- [ ] Logging: detailed server-side, minimal client-side

### 13. Session Fixation Protection
- [ ] Regenerate session after successful login
- [ ] Invalidate old session immediately
- [ ] Don't accept sessionID from query parameters
- [ ] Validate User-Agent consistency (basic device tracking)

### 14. CSRF Protection
- [ ] CSRF tokens for state-changing operations (POST, PUT, DELETE)
- [ ] Token validation: compare request token with session token
- [ ] SameSite cookie flag: reduces CSRF risk
- [ ] Double-submit cookie: alternative token in cookie + body

### 15. Monitoring & Incident Response
- [ ] Alert on suspicious activity (multiple failed logins, impossible travel)
- [ ] Logging: all auth events (login, logout, failed attempt, permission denied)
- [ ] Retention: 90 days minimum
- [ ] Breach response plan: documented
- [ ] Password reset capability: if credentials compromised
- [ ] Account lockdown capability: disable account immediately

---

## Post-Launch Monitoring

### Weekly Reviews
- [ ] Check audit logs for suspicious patterns
- [ ] Verify HTTPS enforcement on all pages
- [ ] Test rate limiting effectiveness

### Monthly Reviews
- [ ] Review failed login attempts (correlate with attacks)
- [ ] Validate RBAC permissions (no privilege creep)
- [ ] Audit API keys in use
- [ ] Check password reset activity

### Quarterly Reviews
- [ ] Security assessment: penetration test
- [ ] Dependency updates: check for CVEs
- [ ] Rotate secrets (OAuth keys, API keys, signing secrets)
- [ ] Review user access patterns (offboard unused accounts)

---

## Common Vulnerabilities (OWASP Top 10)

| Vulnerability | Prevention |
|---|---|
| **Injection** | Parameterized queries, input validation |
| **Broken Auth** | Strong passwords, rate limiting, secure sessions |
| **Sensitive Data Exposure** | HTTPS, encryption at rest, minimize PII logging |
| **Broken Access Control** | RBAC/ABAC, resource ownership validation |
| **CSRF** | CSRF tokens, SameSite cookies |
| **Security Misconfiguration** | Security headers, HTTPS enforcement, secrets management |
| **XSS** | Output sanitization, CSP header |
| **Insecure Deserialization** | Validate all input, use safe serialization |
| **Using Components with Known Vulnerabilities** | Dependency scanning, updates |
| **Insufficient Logging** | Audit all auth events, monitor for anomalies |

---

## Quick Links

- **OWASP Top 10**: https://owasp.org/www-project-top-ten/
- **NIST Password Guidelines**: https://pages.nist.gov/800-63-3/sp800-63b.html
- **RFC 6234 (TOTP)**: https://tools.ietf.org/html/rfc6234
- **JWT Best Practices**: See references/jwt-best-practices.md
- **RBAC Patterns**: See references/rbac-patterns.md

---

## Deployment Checklist

Before deploying to production, ensure:

- [ ] All checklist items above completed
- [ ] Security testing: SAST, DAST, penetration test
- [ ] Load testing: rate limiting doesn't cause false positives
- [ ] Monitoring: alerts configured for suspicious activity
- [ ] Backups: encrypted, regularly tested
- [ ] Documentation: security architecture, incident response plan
- [ ] Team training: all developers understand secure practices
- [ ] Compliance: GDPR, HIPAA, SOC 2 requirements (if applicable)
