# OAuth2 Flows: Choosing the Right One

## Decision Tree

```
Is it a server-side application?
├─ YES → Authorization Code Flow
│        (safest, uses client secret)
│
└─ NO → Is it a single-page app (SPA)?
   ├─ YES → Authorization Code + PKCE
   │        (no client secret, cryptographic challenge)
   │
   └─ NO → Is it a mobile/native app?
      ├─ YES → Authorization Code + PKCE
      │        (same as SPA, can't store secrets)
      │
      └─ NO → Is it machine-to-machine?
         └─ YES → Client Credentials Flow
                  (server-to-server, no user context)
```

## Flow Comparison

| Flow | Security | Complexity | Use Case |
|------|----------|-----------|----------|
| Authorization Code | ⭐⭐⭐⭐⭐ | Medium | Web apps, mobile apps |
| Authorization Code + PKCE | ⭐⭐⭐⭐⭐ | Low | SPAs, mobile apps |
| Client Credentials | ⭐⭐⭐⭐ | Low | Machine-to-machine |
| Implicit (deprecated) | ⭐⭐ | Very Low | ❌ Don't use |
| Password Flow | ⭐⭐⭐ | Low | Legacy/highly trusted apps |

## Authorization Code Flow (Web App)

```
1. User clicks "Login with Google"
2. App redirects to: https://accounts.google.com/o/oauth2/v2/auth?
   client_id=xxx&
   redirect_uri=https://myapp.com/callback&
   scope=openid+email+profile&
   state=random_state_string

3. User authenticates at Google
4. Google redirects to callback with authorization code:
   https://myapp.com/callback?code=auth_code&state=random_state_string

5. Backend exchanges code for token (secure, server-to-server):
   POST /token
   client_id=xxx&
   client_secret=secret&
   code=auth_code&
   grant_type=authorization_code

6. Backend receives: { "access_token": "...", "refresh_token": "..." }
7. Backend sends tokens to frontend (secure HttpOnly cookie for refresh)
```

## Authorization Code + PKCE (SPA/Mobile)

Adds cryptographic proof to prevent authorization code interception:

```typescript
// 1. Generate code verifier (43-128 chars)
const codeVerifier = generateRandomString();

// 2. Generate code challenge (SHA256 hash of verifier)
const codeChallenge = base64url(sha256(codeVerifier));

// 3. Redirect with challenge
https://auth-server.com/authorize?
  client_id=xxx&
  code_challenge=xxx&
  code_challenge_method=S256&  // SHA256
  redirect_uri=...

// 4. Exchange code with verifier (only you have it)
POST /token
  code=auth_code&
  code_verifier=original_verifier&
  client_id=xxx
```

## Client Credentials (Machine-to-Machine)

```typescript
// Service-to-service authentication
POST /token
  grant_type=client_credentials&
  client_id=service_a&
  client_secret=secret&
  scope=api:read+api:write

Response: { "access_token": "...", "expires_in": 3600 }
```

## Best Practices

✓ Always use HTTPS
✓ Validate state parameter to prevent CSRF
✓ Use PKCE for all public clients (SPAs, mobile)
✓ Keep access tokens short-lived (1 hour)
✓ Store refresh tokens in secure HttpOnly cookies
✓ Validate redirect_uri server-side
✓ Use incremental authorization (request scopes when needed)
✓ Implement token rotation on refresh

