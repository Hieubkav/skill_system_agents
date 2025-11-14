# API Documentation Examples

## Example 1: Basic REST API Doc

```bash
# GET request với authentication
curl -X GET "https://api.wincellar.com/v1/products" \
  -H "Authorization: Bearer YOUR_API_KEY"

# Response
{
  "data": [
    {
      "id": "123",
      "name": "Chateau Margaux",
      "price": 199.99
    }
  ],
  "total": 50
}
```

---

## Example 2: GraphQL Query

```graphql
query GetProducts {
  products(limit: 10) {
    id
    name
    price
    vintage
  }
}
```

---

## Example 3: WebSocket Connection

```javascript
const ws = new WebSocket('wss://api.wincellar.com/ws');

ws.onmessage = (event) => {
  const data = JSON.parse(event.data);
  console.log('Real-time update:', data);
};
```

---

## Example 4: POST Request with Body

```bash
curl -X POST "https://api.example.com/v1/orders" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "product_id": "123",
    "quantity": 2,
    "customer": {
      "name": "John Doe",
      "email": "john@example.com"
    }
  }'
```

---

## Example 5: Error Response

```json
{
  "error": {
    "code": "validation_error",
    "message": "Invalid product_id",
    "details": {
      "field": "product_id",
      "reason": "Product not found"
    }
  }
}
```

---

## Example 6: Pagination

```bash
# First page
curl "https://api.example.com/v1/products?limit=20&offset=0"

# Next page
curl "https://api.example.com/v1/products?limit=20&offset=20"
```

---

## Example 7: GraphQL Mutation

```graphql
mutation CreateOrder($input: OrderInput!) {
  createOrder(input: $input) {
    id
    status
    total
    createdAt
  }
}

# Variables
{
  "input": {
    "productId": "123",
    "quantity": 2
  }
}
```

---

## Example 8: Authentication Flow

```bash
# 1. Get token
curl -X POST "https://api.example.com/v1/auth/login" \
  -H "Content-Type: application/json" \
  -d '{"email": "user@example.com", "password": "secret"}'

# Response
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "Bearer",
  "expires_in": 3600
}

# 2. Use token in requests
curl "https://api.example.com/v1/profile" \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
```
