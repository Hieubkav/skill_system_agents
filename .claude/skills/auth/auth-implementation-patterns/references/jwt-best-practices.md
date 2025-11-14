# JWT Best Practices

## Token Structure

```
header.payload.signature
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOiIxMjMiLCJleHAiOjE2ODk3MzY0MDB9.signature
```

## 6 Critical Mistakes to Avoid

### 1. Using Weak Algorithms
❌ HS256 with short secret
✓ RS256 (asymmetric) for distributed systems
✓ EdDSA for newer implementations

### 2. Storing Sensitive Data
❌ "email": "user@example.com" in payload (visible via jwt.io)
✓ Store opaque references: "sub": "a7f8d0c"

### 3. No Token Expiration
❌ No `exp` claim
✓ Access tokens: 15-30 minutes
✓ Refresh tokens: 7 days (stored in secure cookies)

### 4. Ignoring Token Revocation
Problem: JWTs can't be revoked until expiry
Solutions:
- Keep short expiration times
- Maintain Redis blacklist for logged-out tokens
- Use JTI claim for token ID tracking

### 5. Algorithm Confusion Attacks
❌ `jwt.verify(token, secret)` without algorithm check
✓ Explicitly specify allowed algorithms:
```typescript
jwt.verify(token, secret, { algorithms: ['HS256'] });
```

### 6. Not Validating All Claims
Must check: signature, expiration, issuer, audience
```typescript
jwt.verify(token, secret, {
    issuer: 'https://auth.mycompany.com',
    audience: 'my-api'
});
```

## Secure Implementation Checklist

- [ ] Use asymmetric algorithms (RS256, EdDSA) in distributed systems
- [ ] Keep signing secrets in environment variables (not code)
- [ ] Set reasonable expiration times (access: 15m, refresh: 7d)
- [ ] Include JTI claim for token tracking
- [ ] Validate issuer and audience on every request
- [ ] Implement token revocation list for logout
- [ ] Use HTTPS for all token transmission
- [ ] Never store PII in JWT payload
- [ ] Regenerate tokens on privilege changes

