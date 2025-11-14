# Performance Optimization

Comprehensive guide to API performance: caching strategies, ETags, compression, field selection, and batch operations.

## Table of Contents

- [Caching Strategies](#caching-strategies)
- [ETags for Caching](#etags-for-caching)
- [HTTP Compression](#http-compression)
- [Field Selection](#field-selection)
- [Batch Operations](#batch-operations)
- [Database Optimization](#database-optimization)
- [Best Practices](#best-practices)

---

## Caching Strategies

### HTTP Cache Headers

```javascript
// Cache for 1 hour
res.set('Cache-Control', 'public, max-age=3600');

// Cache for 1 day
res.set('Cache-Control', 'public, max-age=86400');

// No cache (always revalidate)
res.set('Cache-Control', 'no-cache');

// Never cache (don't store)
res.set('Cache-Control', 'no-store');

// Private cache (browser only, not CDN)
res.set('Cache-Control', 'private, max-age=3600');
```

### Cache-Control Examples

```javascript
// Static data (rarely changes)
app.get('/api/countries', (req, res) => {
  res.set('Cache-Control', 'public, max-age=86400'); // 1 day
  res.json(countries);
});

// User-specific data
app.get('/api/users/me', (req, res) => {
  res.set('Cache-Control', 'private, max-age=300'); // 5 minutes
  res.json(user);
});

// Sensitive data (never cache)
app.get('/api/payments', (req, res) => {
  res.set('Cache-Control', 'no-store');
  res.json(payments);
});
```

### Redis Caching

```javascript
const redis = require('redis');
const client = redis.createClient();

// Cache middleware
const cache = (duration) => async (req, res, next) => {
  const key = `cache:${req.originalUrl}`;
  
  try {
    const cached = await client.get(key);
    if (cached) {
      return res.json(JSON.parse(cached));
    }
    
    // Store original res.json
    const originalJson = res.json.bind(res);
    
    // Override res.json
    res.json = (data) => {
      client.setEx(key, duration, JSON.stringify(data));
      originalJson(data);
    };
    
    next();
  } catch (err) {
    next();
  }
};

// Usage
app.get('/api/posts', cache(300), getPostsHandler);
```

### Cache Invalidation

```javascript
// Invalidate on create/update/delete
app.post('/api/posts', async (req, res) => {
  const post = await Post.create(req.body);
  
  // Invalidate list cache
  await redis.del('cache:/api/posts');
  await redis.del('cache:/api/posts?*'); // Invalidate all variations
  
  res.status(201).json(post);
});

app.put('/api/posts/:id', async (req, res) => {
  const post = await Post.update(req.params.id, req.body);
  
  // Invalidate specific and list caches
  await redis.del(`cache:/api/posts/${req.params.id}`);
  await redis.del('cache:/api/posts');
  
  res.json(post);
});
```

---

## ETags for Caching

### What are ETags?

ETags (Entity Tags) enable conditional requests. Clients can check if a resource has changed without downloading it again.

### Basic ETag Implementation

```javascript
const crypto = require('crypto');

app.get('/api/users/:id', async (req, res) => {
  const user = await User.findById(req.params.id);
  
  // Generate ETag from content
  const etag = crypto
    .createHash('md5')
    .update(JSON.stringify(user))
    .digest('hex');
  
  // Check If-None-Match header
  if (req.headers['if-none-match'] === etag) {
    return res.status(304).end(); // Not Modified
  }
  
  // Set ETag header
  res.set('ETag', etag);
  res.set('Cache-Control', 'no-cache'); // Always revalidate
  res.json(user);
});
```

### ETag with Last-Modified

```javascript
app.get('/api/users/:id', async (req, res) => {
  const user = await User.findById(req.params.id);
  
  const lastModified = user.updatedAt.toUTCString();
  const ifModifiedSince = req.headers['if-modified-since'];
  
  // Check if resource modified since last request
  if (ifModifiedSince === lastModified) {
    return res.status(304).end();
  }
  
  res.set('Last-Modified', lastModified);
  res.set('Cache-Control', 'no-cache');
  res.json(user);
});
```

### Express ETag Middleware

```javascript
// Express automatically generates ETags
app.set('etag', 'strong'); // or 'weak'

app.get('/api/users/:id', async (req, res) => {
  const user = await User.findById(req.params.id);
  res.json(user); // ETag automatically added
});
```

---

## HTTP Compression

### Enable Compression

```javascript
const compression = require('compression');

// Compress all responses
app.use(compression({
  level: 6, // Compression level (0-9)
  threshold: 1024, // Only compress responses > 1KB
  filter: (req, res) => {
    // Don't compress images
    if (req.headers['x-no-compression']) {
      return false;
    }
    return compression.filter(req, res);
  }
}));
```

### Compression Headers

```http
# Client requests compression
Accept-Encoding: gzip, deflate, br

# Server responds with compression
Content-Encoding: gzip
Vary: Accept-Encoding
```

### Brotli Compression (Better than Gzip)

```javascript
const shrinkRay = require('shrink-ray-current');

app.use(shrinkRay({
  brotli: {
    quality: 4 // 0-11, higher = better compression but slower
  },
  threshold: 1024
}));
```

---

## Field Selection

### Sparse Fieldsets

Allow clients to request only needed fields:

```http
GET /api/v1/users/123?fields=id,email,name

Response:
{
  "id": "user_123",
  "email": "user@example.com",
  "name": "John Doe"
}
```

### Implementation

```javascript
app.get('/api/users/:id', async (req, res) => {
  const user = await User.findById(req.params.id);
  
  // Parse fields parameter
  const fields = req.query.fields?.split(',') || null;
  
  if (fields) {
    // Return only requested fields
    const filtered = {};
    fields.forEach(field => {
      if (user[field] !== undefined) {
        filtered[field] = user[field];
      }
    });
    return res.json(filtered);
  }
  
  res.json(user);
});
```

### Database-Level Field Selection

```javascript
// MongoDB
const user = await User.findById(id).select('email name');

// SQL with Sequelize
const user = await User.findByPk(id, {
  attributes: ['email', 'name']
});

// Laravel Eloquent
$user = User::select(['email', 'name'])->find($id);
```

---

## Batch Operations

### Batch GET Requests

Instead of multiple requests:
```http
GET /api/v1/users/1
GET /api/v1/users/2
GET /api/v1/users/3
```

Use single batch request:
```http
GET /api/v1/users?ids=1,2,3

Response:
{
  "data": [
    { "id": 1, "name": "User 1" },
    { "id": 2, "name": "User 2" },
    { "id": 3, "name": "User 3" }
  ]
}
```

### Batch Mutations

```http
POST /api/v1/users/batch
Content-Type: application/json

{
  "operations": [
    {
      "method": "POST",
      "path": "/api/v1/users",
      "body": { "name": "User 1", "email": "user1@example.com" }
    },
    {
      "method": "PATCH",
      "path": "/api/v1/users/123",
      "body": { "name": "Updated Name" }
    },
    {
      "method": "DELETE",
      "path": "/api/v1/users/456"
    }
  ]
}

Response:
{
  "results": [
    { "status": 201, "body": { "id": 789, "name": "User 1" } },
    { "status": 200, "body": { "id": 123, "name": "Updated Name" } },
    { "status": 204 }
  ]
}
```

---

## Database Optimization

### Indexing

```javascript
// MongoDB indexes
userSchema.index({ email: 1 }, { unique: true });
userSchema.index({ status: 1, createdAt: -1 });
userSchema.index({ name: 'text', bio: 'text' });

// Compound index for common queries
postSchema.index({ authorId: 1, status: 1, createdAt: -1 });
```

### Query Optimization

```javascript
// ❌ Bad - N+1 query problem
const posts = await Post.find();
for (const post of posts) {
  post.author = await User.findById(post.authorId); // N queries
}

// ✅ Good - Join/populate
const posts = await Post.find().populate('author'); // 1-2 queries

// ✅ Good - Batch load
const posts = await Post.find();
const authorIds = posts.map(p => p.authorId);
const authors = await User.find({ _id: { $in: authorIds } });
const authorMap = new Map(authors.map(a => [a.id, a]));
posts.forEach(p => p.author = authorMap.get(p.authorId));
```

### Connection Pooling

```javascript
// MongoDB connection pool
mongoose.connect('mongodb://localhost/mydb', {
  maxPoolSize: 10,
  minPoolSize: 2
});

// PostgreSQL connection pool
const { Pool } = require('pg');
const pool = new Pool({
  max: 20,
  min: 5,
  idleTimeoutMillis: 30000
});
```

### Pagination

```javascript
// ❌ Bad - No limit
const users = await User.find();

// ✅ Good - Paginated
const page = req.query.page || 1;
const limit = Math.min(req.query.limit || 20, 100);
const users = await User.find()
  .skip((page - 1) * limit)
  .limit(limit);
```

---

## Best Practices

### 1. Use CDN for Static Assets

```javascript
// Serve static files through CDN
res.json({
  id: user.id,
  name: user.name,
  avatar: `https://cdn.example.com/avatars/${user.id}.jpg`
});
```

### 2. Implement Response Compression

Always compress text responses (JSON, HTML, CSS, JS).

### 3. Cache Aggressively

```javascript
// Different cache durations for different data
const CACHE_DURATIONS = {
  static: 31536000,    // 1 year
  publicData: 3600,    // 1 hour
  userData: 300,       // 5 minutes
  sensitive: 0         // No cache
};
```

### 4. Use HTTP/2

HTTP/2 provides multiplexing, header compression, and server push.

```javascript
const http2 = require('http2');
const fs = require('fs');

const server = http2.createSecureServer({
  key: fs.readFileSync('server-key.pem'),
  cert: fs.readFileSync('server-cert.pem')
});

server.on('stream', (stream, headers) => {
  stream.respond({
    'content-type': 'application/json',
    ':status': 200
  });
  stream.end(JSON.stringify({ message: 'Hello HTTP/2' }));
});
```

### 5. Minimize Payload Size

```javascript
// Remove null/undefined fields
const cleanObject = (obj) => {
  return Object.entries(obj).reduce((acc, [key, value]) => {
    if (value !== null && value !== undefined) {
      acc[key] = value;
    }
    return acc;
  }, {});
};

res.json(cleanObject(user));
```

### 6. Use GraphQL for Complex Queries

GraphQL eliminates over-fetching and under-fetching:

```graphql
# Request only what you need
query {
  user(id: "123") {
    id
    name
    email
    posts {
      title
      createdAt
    }
  }
}
```

### 7. Implement Rate Limiting

Protect API from abuse and ensure fair usage.

### 8. Monitor Performance

```javascript
const responseTime = require('response-time');

app.use(responseTime((req, res, time) => {
  logger.info('API Request', {
    method: req.method,
    path: req.path,
    duration: time,
    status: res.statusCode
  });
}));
```

---

## Performance Monitoring

### Metrics to Track

- **Response time** - Average, p50, p95, p99
- **Throughput** - Requests per second
- **Error rate** - 4xx and 5xx responses
- **Cache hit rate** - Percentage of cached responses
- **Database query time** - Slow queries
- **Memory usage** - Heap size, garbage collection
- **CPU usage** - Per-endpoint load

### APM Tools

- New Relic
- Datadog
- Application Insights (Azure)
- AWS X-Ray
- Elastic APM

### Custom Monitoring

```javascript
const prometheus = require('prom-client');

// HTTP request duration histogram
const httpDuration = new prometheus.Histogram({
  name: 'http_request_duration_seconds',
  help: 'Duration of HTTP requests in seconds',
  labelNames: ['method', 'route', 'status_code']
});

app.use((req, res, next) => {
  const start = Date.now();
  res.on('finish', () => {
    const duration = (Date.now() - start) / 1000;
    httpDuration.labels(req.method, req.route?.path, res.statusCode).observe(duration);
  });
  next();
});

// Expose metrics endpoint
app.get('/metrics', async (req, res) => {
  res.set('Content-Type', prometheus.register.contentType);
  res.end(await prometheus.register.metrics());
});
```

---

## Load Testing

```javascript
// Using Artillery
// artillery.yml
config:
  target: 'https://api.example.com'
  phases:
    - duration: 60
      arrivalRate: 10
      name: 'Warm up'
    - duration: 300
      arrivalRate: 50
      name: 'Sustained load'

scenarios:
  - name: 'Get users'
    flow:
      - get:
          url: '/api/v1/users'

// Run: artillery run artillery.yml
```

---

## Optimization Checklist

- [ ] Enable HTTP compression (gzip/brotli)
- [ ] Implement caching (Redis, HTTP headers)
- [ ] Use ETags for conditional requests
- [ ] Support field selection (?fields=id,name)
- [ ] Implement pagination (limit/offset or cursor)
- [ ] Use database indexes on filtered/sorted fields
- [ ] Avoid N+1 queries (use joins/batch loading)
- [ ] Use connection pooling
- [ ] Serve static assets through CDN
- [ ] Enable HTTP/2
- [ ] Minimize JSON payload size
- [ ] Rate limit expensive endpoints
- [ ] Monitor response times
- [ ] Set up performance alerts
- [ ] Regular load testing
