---
name: api-documentation-writer
description: Generate comprehensive API documentation for REST, GraphQL, WebSocket APIs with OpenAPI specs, endpoint descriptions, request/response examples, error codes, authentication guides, and SDKs. USE WHEN user says 'viết document API', 'tạo API docs', 'generate API documentation', 'document REST endpoints', hoặc cần tạo technical reference cho developers.
---

# API Documentation Writer

## Level 1: Overview (Always Read First)

Skill này giúp tạo comprehensive API documentation cho REST, GraphQL, WebSocket APIs. Bao gồm OpenAPI specs, endpoint descriptions, request/response examples, authentication guides, error handling, và SDKs.

## Prerequisites

- Access to API source code hoặc specification
- Hiểu rõ API structure (endpoints, authentication, data models)
- Biết API type (REST, GraphQL, WebSocket, gRPC)

## What This Skill Does

1. Gather API information (type, endpoints, authentication, rate limiting)
2. Generate complete documentation structure (overview, auth, endpoints, errors)
3. Create working code examples (cURL, JavaScript, Python)
4. Document error handling và best practices
5. Provide developer-first documentation với realistic examples

---

## Level 2: Quick Start (For Fast Onboarding)

### Basic Workflow

```markdown
1. Gather API Info
   - API type, authentication method, base URL
   - List endpoints và mục đích
   - Rate limiting, webhooks, versioning

2. Generate Structure
   - Overview + Quick Start
   - Authentication guide
   - Each endpoint: method, params, request/response examples
   - Error codes + troubleshooting

3. Format Output
   - REST: OpenAPI/Swagger format
   - GraphQL: Schema documentation
   - WebSocket: Connection + event examples
   - Add code samples (cURL, JS, Python)
```

### Key Principles

- **Developer First** - Copy-paste ready examples
- **Show Before Tell** - Examples trước, giải thích sau
- **Consistent Format** - Mỗi endpoint follow same template
- **Real Data** - Realistic examples, không generic placeholders
- **Multi-Language** - Code examples trong JavaScript, Python, cURL
- **Error Focus** - Include error codes + solutions

---

## Level 3: Reference Guides

- [phase2-structure.md](./references/phase2-structure.md) - Use when: Cần template đầy đủ cho REST/GraphQL API docs
- [examples.md](./references/examples.md) - Use when: Cần tham khảo ví dụ requests/responses
- [common-patterns.md](./references/common-patterns.md) - Use when: Document pagination, webhooks, authentication patterns

---

## When to Activate This Skill

- User nói "viết document API"
- User muốn "tạo API documentation"
- Cần generate "OpenAPI spec"
- Cần document "GraphQL schema"
- Cần tạo "developer guide" cho API
- Cần document "webhook" hoặc "authentication"

---

## Core Workflow

### Phase 1: Gather API Information

Hỏi user những câu hỏi sau để hiểu rõ API:
- **API type**: REST, GraphQL, WebSocket, gRPC?
- **Authentication**: API key, OAuth, JWT, Bearer token?
- **Base URL**: Production URL, versioning strategy?
- **Endpoints**: Danh sách endpoints, mục đích của từng cái?
- **Request/Response format**: JSON, XML, custom format?
- **Rate limiting**: Có giới hạn không, bao nhiêu requests/giờ?
- **Webhooks**: Có support webhooks không?

### Phase 2: Generate Documentation Structure

**Must Include**:
- Overview Section (mô tả API, key capabilities, quick start)
- Authentication (cách lấy credentials, example authenticated request)
- Base URL & Versioning
- Endpoints (method, params, request/response examples, error codes)
- Error Handling (standard error format, common codes)
- Rate Limiting (limits, headers, handling)
- SDKs & Libraries (official/community clients)
- Webhooks (events, setup, payload examples)

### Phase 3: Format Output

Use appropriate template:
- **REST API**: OpenAPI/Swagger format
- **GraphQL**: Schema documentation standard
- **WebSocket**: Connection guide + event examples

Add code examples: cURL, JavaScript, Python

---

## Documentation Quality Checklist

- [ ] Starts with working example
- [ ] Explains every parameter
- [ ] Shows realistic request/response examples
- [ ] Includes error handling
- [ ] Provides code samples (3+ languages)
- [ ] Uses consistent formatting
- [ ] Organized logically (common operations first)
- [ ] Authentication documented clearly
- [ ] Covers edge cases + limitations
- [ ] Scannable with good headers

---

## Best Practices

1. **Quick Start First** - Working example trong 60 giây
2. **Realistic Examples** - Use real-world data, không "foo" / "bar"
3. **Complete Responses** - Show full JSON responses
4. **Error Documentation** - Include error codes + meanings + solutions
5. **Multi-Language Support** - At least 3 language examples
6. **Consistent Format** - Same structure cho mỗi endpoint
7. **Interactive Examples** - Postman Collection / OpenAPI spec

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Missing authentication details | Ask user về auth method, credential location, token format |
| Incomplete endpoint list | Request API spec file, route list, hoặc explore codebase |
| No error examples | Generate standard error formats, ask về custom error codes |
| Rate limit unclear | Ask về limits, windows, headers, retry strategies |
| Complex nested responses | Break down into smaller examples, explain each field |

---

## Common Use Cases

### Case 1: Document Existing REST API
- Extract endpoints từ routes file
- Generate OpenAPI spec
- Add code examples cho mỗi endpoint
- Include authentication guide

### Case 2: Create GraphQL Schema Docs
- Document queries, mutations, subscriptions
- Show resolver examples
- Include variables + directives
- Explain error handling

### Case 3: Document WebSocket API
- Connection setup guide
- Available events + payloads
- Example client implementations
- Reconnection strategies

---

## Related Skills

- **api-design-patterns**: Sử dụng cho API design + architecture
- **api-cache-invalidation**: Sync data giữa frontend-backend
