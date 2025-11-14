# Common API Documentation Patterns

## Pattern 1: REST API with Pagination

Document pagination approach:

### Offset-Based Pagination

```bash
GET /api/v1/products?limit=20&offset=40
```

**Response**:
```json
{
  "data": [...],
  "pagination": {
    "limit": 20,
    "offset": 40,
    "total": 1000,
    "has_more": true
  }
}
```

### Cursor-Based Pagination

```bash
GET /api/v1/products?limit=20&cursor=eyJpZCI6MTAwfQ
```

**Response**:
```json
{
  "data": [...],
  "pagination": {
    "next_cursor": "eyJpZCI6MTIwfQ",
    "has_more": true
  }
}
```

### Page-Based Pagination

```bash
GET /api/v1/products?page=3&per_page=20
```

---

## Pattern 2: GraphQL Schema Documentation

Document schema với:

### Type Definitions

```graphql
type Product {
  id: ID!
  name: String!
  price: Float!
  description: String
  category: Category
  inStock: Boolean!
  createdAt: DateTime!
}

type Category {
  id: ID!
  name: String!
  products: [Product!]!
}
```

### Query Examples

```graphql
query GetProduct($id: ID!) {
  product(id: $id) {
    id
    name
    price
    category {
      name
    }
  }
}
```

### Mutation Examples

```graphql
mutation UpdateProduct($id: ID!, $input: ProductInput!) {
  updateProduct(id: $id, input: $input) {
    id
    name
    price
  }
}
```

### Error Handling

```graphql
{
  "errors": [
    {
      "message": "Product not found",
      "extensions": {
        "code": "NOT_FOUND",
        "id": "123"
      }
    }
  ]
}
```

---

## Pattern 3: Webhook Documentation

### Available Events

```markdown
| Event | Description | Trigger |
|-------|-------------|---------|
| `order.created` | New order placed | When customer completes checkout |
| `order.updated` | Order status changed | When admin updates order |
| `product.created` | New product added | When admin creates product |
```

### Payload Example

```json
{
  "event": "order.created",
  "timestamp": "2025-11-14T10:00:00Z",
  "data": {
    "order_id": "ord_123456",
    "customer": {
      "id": "cust_789",
      "email": "customer@example.com"
    },
    "total": 199.99,
    "items": [...]
  }
}
```

### Signature Verification

```javascript
const crypto = require('crypto');

function verifyWebhook(payload, signature, secret) {
  const hmac = crypto.createHmac('sha256', secret);
  const digest = hmac.update(payload).digest('hex');
  return signature === digest;
}
```

### Setup Process

```markdown
1. Create webhook endpoint in your dashboard
2. Configure URL and events
3. Save webhook secret
4. Verify signature on incoming requests
5. Return 200 OK to acknowledge receipt
```

### Retry Logic

```markdown
- Failed webhooks are retried 3 times
- Retry intervals: 1min, 5min, 30min
- After 3 failures, webhook is marked as failed
- View failed webhooks in dashboard
```

### Testing Environment

```bash
# Test webhook with cURL
curl -X POST "https://your-app.com/webhooks" \
  -H "Content-Type: application/json" \
  -H "X-Webhook-Signature: abc123..." \
  -d '{
    "event": "order.created",
    "data": {...}
  }'
```

---

## Pattern 4: Authentication Methods

### API Key Authentication

```bash
curl "https://api.example.com/v1/products" \
  -H "X-API-Key: your_api_key_here"
```

### Bearer Token (JWT)

```bash
curl "https://api.example.com/v1/products" \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
```

### OAuth 2.0 Flow

```markdown
1. Redirect user to authorization URL
2. User approves access
3. Get authorization code
4. Exchange code for access token
5. Use access token in API requests
```

---

## Pattern 5: Filtering & Sorting

### Filtering

```bash
# Single filter
GET /api/v1/products?category=wine

# Multiple filters
GET /api/v1/products?category=wine&price_min=50&price_max=200

# Complex filters
GET /api/v1/products?filter[category]=wine&filter[inStock]=true
```

### Sorting

```bash
# Single sort
GET /api/v1/products?sort=price

# Descending sort
GET /api/v1/products?sort=-price

# Multiple sorts
GET /api/v1/products?sort=category,-price
```

---

## Pattern 6: Rate Limiting Documentation

### Rate Limit Headers

```
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 999
X-RateLimit-Reset: 1699956789
```

### Rate Limit Response

```json
{
  "error": {
    "code": "rate_limit_exceeded",
    "message": "Rate limit exceeded. Retry after 60 seconds.",
    "retry_after": 60
  }
}
```

### Best Practices

```markdown
- Check X-RateLimit-Remaining header
- Implement exponential backoff
- Cache responses when possible
- Use webhooks instead of polling
```
