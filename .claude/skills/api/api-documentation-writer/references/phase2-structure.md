# Phase 2: Complete Documentation Structure

## REST API Template

```markdown
# [API Name] Documentation

## Overview

[Brief description]

**Base URL**: `https://api.example.com/v1`

**Authentication**: API Key via Authorization header

## Quick Start

1. [Step 1]
2. [Step 2]
3. [Step 3]

## Authentication

All requests require API key:

```
Authorization: Bearer YOUR_API_KEY
```

## Endpoints

### GET /resource

Retrieve list of resources.

**Parameters**:
- `limit` (optional, integer): Number of results
- `offset` (optional, integer): Pagination offset

**Request Example**:
```bash
curl -X GET "https://api.example.com/v1/resource?limit=10" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

**Response** (200 OK):
```json
{
  "data": [...],
  "total": 100,
  "limit": 10
}
```

## Error Handling

Standard error format:
```json
{
  "error": {
    "code": "invalid_request",
    "message": "The 'name' field is required"
  }
}
```

## Support

- Documentation: [url]
- Support: [email]
```

---

## GraphQL Template

Adjust để show:
- Schema definitions
- Query examples
- Mutation examples
- Subscription examples
- Variables + directives

---

## Documentation Best Practices

- ✅ Start với working example (copy-paste ready)
- ✅ Show cả request và response
- ✅ Use realistic example data
- ✅ Include error cases
- ✅ Explain mỗi parameter
- ✅ Provide code examples (JavaScript, Python, PHP)
- ✅ Use consistent formatting
- ✅ Add interactive examples (nếu possible)
- ✅ Link related endpoints
- ✅ Include changelog + versioning

---

## Developer Experience Tips

- **Quick Start**: Working example trong 60 giây
- **Postman Collection**: Include OpenAPI/Swagger spec
- **Common Use Cases**: Show realistic workflows
- **Troubleshooting**: FAQ section
- **Testing Environment**: Sandbox/test URLs
- **SDKs**: Installation instructions
- **Rate Limiting**: Details upfront
- **Pagination**: Show patterns
- **Filtering & Sorting**: Explain options

---

## Available Tools / Commands

- **REST APIs**: Use OpenAPI/Swagger format
- **GraphQL**: Use GraphQL Schema Documentation standard
- **Webhooks**: Include setup guide + payload examples
- **Authentication**: Clear examples cho mỗi method
- **Code Examples**: Cung cấp JavaScript, Python, cURL

---

## Key Principles

1. **Developer First**: Giả định developers muốn copy-paste examples
2. **Show Before Tell**: Ví dụ trước, sau đó mới giải thích
3. **Consistent Format**: Mỗi endpoint follow cùng template
4. **Error Focus**: Include error codes + solutions
5. **Real Data**: Use realistic examples, không generic placeholders
6. **Scannable**: Headers + short descriptions cho quick lookup
7. **Complete**: Cover happy path + edge cases
8. **Multi-Language**: Code examples trong JavaScript, Python, cURL
