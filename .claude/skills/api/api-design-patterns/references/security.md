# API Security Best Practices

Comprehensive guide to securing APIs: input validation, SQL injection prevention, CORS, HTTPS, and security headers.

## Table of Contents

- [Always Use HTTPS](#always-use-https)
- [Input Validation](#input-validation)
- [SQL Injection Prevention](#sql-injection-prevention)
- [CORS Configuration](#cors-configuration)
- [Security Headers](#security-headers)
- [Rate Limiting](#rate-limiting)
- [Security Logging](#security-logging)
- [Security Checklist](#security-checklist)

---

## Always Use HTTPS

### Why HTTPS is Critical

- Encrypts data in transit
- Prevents man-in-the-middle attacks
- Required for modern authentication (JWT, OAuth)
- Builds user trust
- Required by browsers for many APIs

### Enforce HTTPS

**Node.js/Express:**
```javascript
// Redirect HTTP to HTTPS
app.use((req, res, next) => {
  if (req.header('x-forwarded-proto') !== 'https' && process.env.NODE_ENV === 'production') {
    return res.redirect(`https://${req.header('host')}${req.url}`);
  }
  next();
});

// Or use helmet middleware
const helmet = require('helmet');
app.use(helmet.hsts({
  maxAge: 31536000, // 1 year
  includeSubDomains: true,
  preload: true
}));
```

**Laravel:**
```php
// In middleware
if (!$request->secure() && app()->environment('production')) {
    return redirect()->secure($request->getRequestUri());
}
```

**Nginx:**
```nginx
server {
    listen 80;
    server_name api.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name api.example.com;
    
    ssl_certificate /path/to/cert.pem;
    ssl_certificate_key /path/to/key.pem;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;
}
```

---

## Input Validation

### Validate All Inputs

**Never trust user input. Validate everything.**

```javascript
const Joi = require('joi');

const userSchema = Joi.object({
  email: Joi.string().email().required(),
  password: Joi.string().min(8).max(100).required(),
  age: Joi.number().integer().min(18).max(120),
  website: Joi.string().uri().optional()
});

const { error, value } = userSchema.validate(req.body);
if (error) {
  return res.status(400).json({ error: error.details });
}
```

### Sanitize Outputs

Always escape user input when displaying:

```javascript
const escapeHtml = require('escape-html');

const safeOutput = escapeHtml(userInput);
```

### Whitelist, Don't Blacklist

```javascript
// ✅ Good - Whitelist allowed values
const ALLOWED_ROLES = ['user', 'admin', 'moderator'];
if (!ALLOWED_ROLES.includes(req.body.role)) {
  return res.status(400).json({ error: 'Invalid role' });
}

// ❌ Bad - Blacklist dangerous values (incomplete)
const FORBIDDEN_ROLES = ['superadmin'];
if (FORBIDDEN_ROLES.includes(req.body.role)) {
  return res.status(400).json({ error: 'Invalid role' });
}
```

---

## SQL Injection Prevention

### Use Parameterized Queries

**Always use parameterized queries or ORMs.**

```javascript
// ✅ SAFE - Parameterized query
const user = await db.query(
  'SELECT * FROM users WHERE email = ?',
  [email]
);

// ❌ UNSAFE - String concatenation
const user = await db.query(
  `SELECT * FROM users WHERE email = '${email}'`
);
```

### ORM Examples

**Sequelize (Node.js):**
```javascript
// ✅ Safe - ORM handles parameterization
const user = await User.findOne({
  where: { email: email }
});
```

**Laravel Eloquent:**
```php
// ✅ Safe - Eloquent uses prepared statements
$user = User::where('email', $email)->first();

// ✅ Safe - Query builder with bindings
$user = DB::table('users')
    ->where('email', '=', $email)
    ->first();

// ❌ UNSAFE - Raw query with concatenation
$user = DB::select("SELECT * FROM users WHERE email = '$email'");
```

**Django ORM:**
```python
# ✅ Safe - ORM parameterizes
user = User.objects.get(email=email)

# ✅ Safe - Raw query with parameters
User.objects.raw('SELECT * FROM users WHERE email = %s', [email])

# ❌ UNSAFE - String formatting
User.objects.raw(f'SELECT * FROM users WHERE email = {email}')
```

### NoSQL Injection Prevention

**MongoDB:**
```javascript
// ✅ Safe - Type checking
const userId = parseInt(req.params.id);
if (isNaN(userId)) {
  return res.status(400).json({ error: 'Invalid ID' });
}

// ✅ Safe - Validate object structure
const filter = { 
  email: String(req.body.email),
  role: String(req.body.role)
};

// ❌ UNSAFE - Accepting arbitrary objects
const filter = req.body; // Can inject operators like $where
```

---

## CORS Configuration

### Production CORS Setup

```javascript
const cors = require('cors');

// ✅ Good - Specific origins
app.use(cors({
  origin: [
    'https://myapp.com',
    'https://app.myapp.com',
    'https://admin.myapp.com'
  ],
  credentials: true,
  methods: ['GET', 'POST', 'PUT', 'DELETE', 'PATCH'],
  allowedHeaders: ['Content-Type', 'Authorization'],
  maxAge: 86400 // Cache preflight for 24 hours
}));

// ❌ DANGEROUS - Wildcard in production
app.use(cors({ origin: '*' }));
```

### Dynamic CORS (Multiple Environments)

```javascript
const allowedOrigins = {
  development: ['http://localhost:3000', 'http://localhost:3001'],
  production: ['https://myapp.com', 'https://app.myapp.com']
};

app.use(cors({
  origin: (origin, callback) => {
    const allowed = allowedOrigins[process.env.NODE_ENV] || [];
    if (!origin || allowed.includes(origin)) {
      callback(null, true);
    } else {
      callback(new Error('Not allowed by CORS'));
    }
  },
  credentials: true
}));
```

### CORS Headers

```http
Access-Control-Allow-Origin: https://myapp.com
Access-Control-Allow-Methods: GET, POST, PUT, DELETE
Access-Control-Allow-Headers: Content-Type, Authorization
Access-Control-Allow-Credentials: true
Access-Control-Max-Age: 86400
```

---

## Security Headers

### Essential Security Headers

```javascript
const helmet = require('helmet');

app.use(helmet({
  // Prevent clickjacking
  frameguard: { action: 'deny' },
  
  // Prevent MIME sniffing
  noSniff: true,
  
  // Enforce HTTPS
  hsts: {
    maxAge: 31536000,
    includeSubDomains: true,
    preload: true
  },
  
  // XSS protection
  xssFilter: true,
  
  // Content Security Policy
  contentSecurityPolicy: {
    directives: {
      defaultSrc: ["'self'"],
      styleSrc: ["'self'", "'unsafe-inline'"],
      scriptSrc: ["'self'"],
      imgSrc: ["'self'", "data:", "https:"]
    }
  }
}));
```

### Manual Headers

```javascript
// Prevent clickjacking
res.setHeader('X-Frame-Options', 'DENY');

// Prevent MIME sniffing
res.setHeader('X-Content-Type-Options', 'nosniff');

// XSS protection
res.setHeader('X-XSS-Protection', '1; mode=block');

// Referrer policy
res.setHeader('Referrer-Policy', 'strict-origin-when-cross-origin');

// Permissions policy
res.setHeader('Permissions-Policy', 'geolocation=(), microphone=()');
```

---

## Rate Limiting

### Implementation

```javascript
const rateLimit = require('express-rate-limit');

// General API rate limit
const apiLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // 100 requests per window
  message: 'Too many requests, please try again later',
  standardHeaders: true,
  legacyHeaders: false
});

app.use('/api/', apiLimiter);

// Stricter limit for authentication
const authLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 5,
  skipSuccessfulRequests: true
});

app.post('/api/auth/login', authLimiter, loginHandler);
```

### Rate Limit Headers

```http
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1634400000
```

### Redis-Based Rate Limiting

```javascript
const Redis = require('ioredis');
const redis = new Redis();

const checkRateLimit = async (userId, limit = 100, window = 3600) => {
  const key = `ratelimit:${userId}`;
  const current = await redis.incr(key);
  
  if (current === 1) {
    await redis.expire(key, window);
  }
  
  if (current > limit) {
    const ttl = await redis.ttl(key);
    throw {
      status: 429,
      message: 'Rate limit exceeded',
      retryAfter: ttl
    };
  }
  
  return {
    limit,
    remaining: limit - current,
    reset: Date.now() + (await redis.ttl(key)) * 1000
  };
};
```

---

## Security Logging

### Log Security Events

```javascript
const logger = require('./logger');

// Failed login attempts
logger.warn('Failed login attempt', {
  email: email,
  ip: req.ip,
  userAgent: req.headers['user-agent'],
  timestamp: new Date()
});

// Multiple failed attempts
logger.critical('Multiple failed login attempts detected', {
  email: email,
  ip: req.ip,
  attempts: 5
});

// Privilege escalation attempts
logger.critical('Unauthorized access attempt', {
  userId: req.user.id,
  attemptedRole: req.body.role,
  currentRole: req.user.role,
  endpoint: req.path
});

// Suspicious patterns
logger.warning('Suspicious API usage pattern', {
  userId: req.user.id,
  requestsLastMinute: 500,
  endpoint: req.path
});
```

### What to Log

**✅ Do Log:**
- Failed login attempts
- Privilege escalation attempts
- Unusual API usage patterns
- Access to sensitive resources
- Rate limit violations
- Security configuration changes
- Data export operations

**❌ Never Log:**
- Passwords (plain or hashed)
- Access tokens or API keys
- Credit card numbers
- Social security numbers
- Private encryption keys
- User's full personal data

---

## Security Checklist

### Authentication & Authorization
- [ ] Use HTTPS everywhere
- [ ] Hash passwords with bcrypt (cost 10-12)
- [ ] Implement JWT with short expiration (15 min)
- [ ] Use refresh tokens for long sessions
- [ ] Store tokens in HttpOnly cookies
- [ ] Implement CSRF protection
- [ ] Rate limit auth endpoints (5/15min)
- [ ] Log failed login attempts
- [ ] Implement account lockout
- [ ] Support MFA for sensitive accounts

### Input Validation
- [ ] Validate all user inputs
- [ ] Use parameterized queries
- [ ] Sanitize outputs (escape HTML)
- [ ] Whitelist allowed values
- [ ] Validate file uploads (type, size)
- [ ] Check file content, not just extension
- [ ] Limit request body size

### Configuration
- [ ] Set proper CORS origins
- [ ] Use security headers (helmet)
- [ ] Disable unnecessary HTTP methods
- [ ] Hide server version headers
- [ ] Use environment variables for secrets
- [ ] Never commit secrets to git
- [ ] Rotate API keys regularly

### API Design
- [ ] Rate limit all endpoints
- [ ] Implement idempotency keys
- [ ] Validate API versions
- [ ] Return appropriate status codes
- [ ] Don't leak sensitive errors
- [ ] Implement proper error handling
- [ ] Use pagination for large datasets

### Monitoring & Logging
- [ ] Log security events
- [ ] Monitor for anomalies
- [ ] Set up alerts for suspicious activity
- [ ] Never log sensitive data
- [ ] Implement audit trails
- [ ] Regular security reviews
- [ ] Keep dependencies updated

### Data Protection
- [ ] Encrypt sensitive data at rest
- [ ] Use HTTPS for data in transit
- [ ] Implement proper access controls
- [ ] Minimize data collection
- [ ] Implement data retention policies
- [ ] Support data deletion requests
- [ ] Regular database backups

---

## Common Vulnerabilities

### SQL Injection
```javascript
// ❌ Vulnerable
db.query(`SELECT * FROM users WHERE id = ${userId}`);

// ✅ Safe
db.query('SELECT * FROM users WHERE id = ?', [userId]);
```

### XSS (Cross-Site Scripting)
```javascript
// ❌ Vulnerable
res.send(`<div>Welcome ${username}</div>`);

// ✅ Safe
res.send(`<div>Welcome ${escapeHtml(username)}</div>`);
```

### CSRF (Cross-Site Request Forgery)
```javascript
// ✅ Use CSRF tokens
const csrf = require('csurf');
app.use(csrf({ cookie: true }));

app.get('/api/csrf-token', (req, res) => {
  res.json({ csrfToken: req.csrfToken() });
});
```

### Insecure Direct Object References
```javascript
// ❌ Vulnerable - No authorization check
app.get('/api/users/:id', async (req, res) => {
  const user = await User.findById(req.params.id);
  res.json(user);
});

// ✅ Safe - Check ownership
app.get('/api/users/:id', async (req, res) => {
  if (req.user.id !== req.params.id && !req.user.isAdmin) {
    return res.status(403).json({ error: 'Forbidden' });
  }
  const user = await User.findById(req.params.id);
  res.json(user);
});
```

### Mass Assignment
```javascript
// ❌ Vulnerable - Can set any field
const user = await User.create(req.body);

// ✅ Safe - Whitelist fields
const { email, name, age } = req.body;
const user = await User.create({ email, name, age });
```

---

## Security Testing

```javascript
describe('Security', () => {
  test('should reject SQL injection attempts', async () => {
    const res = await request(app)
      .get('/api/users?id=1 OR 1=1');
    expect(res.status).toBe(400);
  });

  test('should rate limit authentication', async () => {
    for (let i = 0; i < 6; i++) {
      await request(app)
        .post('/api/auth/login')
        .send({ email: 'test@example.com', password: 'wrong' });
    }
    
    const res = await request(app)
      .post('/api/auth/login')
      .send({ email: 'test@example.com', password: 'correct' });
    
    expect(res.status).toBe(429);
  });

  test('should enforce CORS', async () => {
    const res = await request(app)
      .get('/api/users')
      .set('Origin', 'https://malicious.com');
    
    expect(res.headers['access-control-allow-origin']).toBeUndefined();
  });
});
```
