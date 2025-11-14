# OpenAPI / Swagger Documentation

Comprehensive guide to API documentation using OpenAPI 3.0 specification and Swagger tools.

## Table of Contents

- [OpenAPI Basics](#openapi-basics)
- [Document Structure](#document-structure)
- [Schemas and Models](#schemas-and-models)
- [Authentication](#authentication)
- [Examples](#examples)
- [Tools and Generators](#tools-and-generators)

---

## OpenAPI Basics

### What is OpenAPI?

OpenAPI (formerly Swagger) is a specification for describing REST APIs. It provides:
- Machine-readable API documentation
- Interactive API explorer
- Code generation for clients and servers
- API testing and validation

### OpenAPI 3.0 Structure

```yaml
openapi: 3.0.0
info:          # API metadata
servers:       # API servers
paths:         # API endpoints
components:    # Reusable components
  schemas:     # Data models
  responses:   # Reusable responses
  parameters:  # Reusable parameters
  securitySchemes: # Auth methods
security:      # Global security
tags:          # Endpoint grouping
```

---

## Document Structure

### Basic OpenAPI Document

```yaml
openapi: 3.0.0

info:
  title: My API
  version: 1.0.0
  description: |
    Comprehensive API for managing users and posts.
    
    ## Authentication
    Use Bearer token in Authorization header.
    
    ## Rate Limiting
    100 requests per minute.
  contact:
    name: API Support
    email: support@example.com
    url: https://example.com/support
  license:
    name: MIT
    url: https://opensource.org/licenses/MIT

servers:
  - url: https://api.example.com/v1
    description: Production server
  - url: https://staging-api.example.com/v1
    description: Staging server
  - url: http://localhost:3000/v1
    description: Development server

tags:
  - name: Users
    description: User management endpoints
  - name: Posts
    description: Blog post endpoints

paths:
  # Endpoints defined here
  
components:
  # Reusable components defined here
```

---

## Paths and Operations

### Basic Endpoint

```yaml
paths:
  /users:
    get:
      summary: List users
      description: Retrieve a paginated list of users
      tags:
        - Users
      parameters:
        - name: page
          in: query
          description: Page number
          required: false
          schema:
            type: integer
            default: 1
            minimum: 1
        - name: limit
          in: query
          description: Items per page
          required: false
          schema:
            type: integer
            default: 20
            minimum: 1
            maximum: 100
      responses:
        '200':
          description: Successful response
          content:
            application/json:
              schema:
                type: object
                properties:
                  data:
                    type: array
                    items:
                      $ref: '#/components/schemas/User'
                  pagination:
                    $ref: '#/components/schemas/Pagination'
        '400':
          $ref: '#/components/responses/BadRequest'
        '401':
          $ref: '#/components/responses/Unauthorized'
```

### POST Endpoint

```yaml
paths:
  /users:
    post:
      summary: Create user
      description: Create a new user account
      tags:
        - Users
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateUserRequest'
            examples:
              example1:
                summary: Basic user
                value:
                  email: user@example.com
                  name: John Doe
                  password: SecurePass123!
      responses:
        '201':
          description: User created successfully
          headers:
            Location:
              description: URL of created user
              schema:
                type: string
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'
        '400':
          $ref: '#/components/responses/ValidationError'
        '409':
          description: Email already exists
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'
```

### Path Parameters

```yaml
paths:
  /users/{userId}:
    parameters:
      - name: userId
        in: path
        required: true
        description: User ID
        schema:
          type: string
          pattern: '^user_[a-zA-Z0-9]+$'
    
    get:
      summary: Get user
      tags:
        - Users
      responses:
        '200':
          description: User found
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'
        '404':
          $ref: '#/components/responses/NotFound'
    
    put:
      summary: Update user
      tags:
        - Users
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/UpdateUserRequest'
      responses:
        '200':
          description: User updated
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'
        '404':
          $ref: '#/components/responses/NotFound'
    
    delete:
      summary: Delete user
      tags:
        - Users
      responses:
        '204':
          description: User deleted
        '404':
          $ref: '#/components/responses/NotFound'
```

---

## Schemas and Models

### User Schema

```yaml
components:
  schemas:
    User:
      type: object
      required:
        - id
        - email
        - name
      properties:
        id:
          type: string
          example: user_abc123
          readOnly: true
        email:
          type: string
          format: email
          example: user@example.com
        name:
          type: string
          minLength: 1
          maxLength: 100
          example: John Doe
        age:
          type: integer
          minimum: 18
          maximum: 120
          example: 30
        role:
          type: string
          enum: [user, admin, moderator]
          default: user
        avatar:
          type: string
          format: uri
          nullable: true
          example: https://cdn.example.com/avatars/user_abc123.jpg
        createdAt:
          type: string
          format: date-time
          readOnly: true
          example: 2025-10-16T10:30:00Z
        updatedAt:
          type: string
          format: date-time
          readOnly: true
          example: 2025-10-16T10:30:00Z
```

### Request Schemas

```yaml
components:
  schemas:
    CreateUserRequest:
      type: object
      required:
        - email
        - name
        - password
      properties:
        email:
          type: string
          format: email
        name:
          type: string
          minLength: 1
          maxLength: 100
        password:
          type: string
          format: password
          minLength: 8
          maxLength: 100
        age:
          type: integer
          minimum: 18

    UpdateUserRequest:
      type: object
      properties:
        email:
          type: string
          format: email
        name:
          type: string
          minLength: 1
          maxLength: 100
        age:
          type: integer
          minimum: 18
```

### Error Schema

```yaml
components:
  schemas:
    Error:
      type: object
      required:
        - code
        - message
      properties:
        code:
          type: string
          example: VALIDATION_ERROR
        message:
          type: string
          example: Request validation failed
        field:
          type: string
          example: email
        details:
          type: object
          additionalProperties: true

    ValidationError:
      type: object
      required:
        - code
        - message
        - errors
      properties:
        code:
          type: string
          example: VALIDATION_ERROR
        message:
          type: string
          example: Request validation failed
        errors:
          type: array
          items:
            type: object
            properties:
              field:
                type: string
                example: email
              message:
                type: string
                example: Email is required
              code:
                type: string
                example: REQUIRED_FIELD
```

### Pagination Schema

```yaml
components:
  schemas:
    Pagination:
      type: object
      properties:
        page:
          type: integer
          example: 2
        limit:
          type: integer
          example: 20
        total:
          type: integer
          example: 156
        totalPages:
          type: integer
          example: 8
        hasNext:
          type: boolean
          example: true
        hasPrev:
          type: boolean
          example: true
```

---

## Authentication

### Bearer Token (JWT)

```yaml
components:
  securitySchemes:
    BearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT
      description: |
        JWT token obtained from /auth/login endpoint.
        Format: Bearer {token}

security:
  - BearerAuth: []

paths:
  /auth/login:
    post:
      summary: Login
      tags:
        - Authentication
      security: []  # No authentication required
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              required:
                - email
                - password
              properties:
                email:
                  type: string
                  format: email
                password:
                  type: string
                  format: password
      responses:
        '200':
          description: Login successful
          content:
            application/json:
              schema:
                type: object
                properties:
                  accessToken:
                    type: string
                  refreshToken:
                    type: string
                  expiresIn:
                    type: integer
                    example: 900
```

### API Key

```yaml
components:
  securitySchemes:
    ApiKeyAuth:
      type: apiKey
      in: header
      name: X-API-Key
      description: API key for authentication

security:
  - ApiKeyAuth: []
```

### OAuth 2.0

```yaml
components:
  securitySchemes:
    OAuth2:
      type: oauth2
      flows:
        authorizationCode:
          authorizationUrl: https://auth.example.com/oauth/authorize
          tokenUrl: https://auth.example.com/oauth/token
          scopes:
            read:users: Read user data
            write:users: Modify user data
            read:posts: Read posts
            write:posts: Create and modify posts

security:
  - OAuth2: [read:users, write:users]
```

---

## Reusable Components

### Responses

```yaml
components:
  responses:
    BadRequest:
      description: Bad request
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'

    Unauthorized:
      description: Unauthorized
      content:
        application/json:
          schema:
            type: object
            properties:
              error:
                type: object
                properties:
                  code:
                    type: string
                    example: UNAUTHORIZED
                  message:
                    type: string
                    example: Authentication required

    NotFound:
      description: Resource not found
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'

    ValidationError:
      description: Validation error
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/ValidationError'
```

### Parameters

```yaml
components:
  parameters:
    PageParam:
      name: page
      in: query
      description: Page number
      schema:
        type: integer
        default: 1
        minimum: 1

    LimitParam:
      name: limit
      in: query
      description: Items per page
      schema:
        type: integer
        default: 20
        minimum: 1
        maximum: 100

    SortParam:
      name: sort
      in: query
      description: Sort field (prefix with - for descending)
      schema:
        type: string
        example: -created_at

# Use in paths
paths:
  /users:
    get:
      parameters:
        - $ref: '#/components/parameters/PageParam'
        - $ref: '#/components/parameters/LimitParam'
        - $ref: '#/components/parameters/SortParam'
```

---

## Examples

### Complete Example

```yaml
openapi: 3.0.0

info:
  title: Blog API
  version: 1.0.0
  description: RESTful API for blog management

servers:
  - url: https://api.example.com/v1

tags:
  - name: Posts
    description: Blog post operations

paths:
  /posts:
    get:
      summary: List posts
      tags:
        - Posts
      parameters:
        - name: status
          in: query
          schema:
            type: string
            enum: [draft, published, archived]
        - name: page
          in: query
          schema:
            type: integer
            default: 1
      responses:
        '200':
          description: Success
          content:
            application/json:
              schema:
                type: object
                properties:
                  data:
                    type: array
                    items:
                      $ref: '#/components/schemas/Post'
                  pagination:
                    $ref: '#/components/schemas/Pagination'

    post:
      summary: Create post
      tags:
        - Posts
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreatePostRequest'
      responses:
        '201':
          description: Post created
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Post'

  /posts/{postId}:
    get:
      summary: Get post
      tags:
        - Posts
      parameters:
        - name: postId
          in: path
          required: true
          schema:
            type: string
      responses:
        '200':
          description: Success
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Post'
        '404':
          $ref: '#/components/responses/NotFound'

components:
  schemas:
    Post:
      type: object
      properties:
        id:
          type: string
        title:
          type: string
        content:
          type: string
        status:
          type: string
          enum: [draft, published, archived]
        author:
          $ref: '#/components/schemas/User'
        createdAt:
          type: string
          format: date-time

    User:
      type: object
      properties:
        id:
          type: string
        name:
          type: string
        email:
          type: string
          format: email

    CreatePostRequest:
      type: object
      required:
        - title
        - content
      properties:
        title:
          type: string
        content:
          type: string
        status:
          type: string
          enum: [draft, published]
          default: draft

    Pagination:
      type: object
      properties:
        page:
          type: integer
        totalPages:
          type: integer
        total:
          type: integer

  responses:
    NotFound:
      description: Not found
      content:
        application/json:
          schema:
            type: object
            properties:
              error:
                type: string

  securitySchemes:
    BearerAuth:
      type: http
      scheme: bearer

security:
  - BearerAuth: []
```

---

## Tools and Generators

### Swagger UI

Interactive API documentation:

```javascript
const swaggerUi = require('swagger-ui-express');
const YAML = require('yamljs');

const swaggerDocument = YAML.load('./openapi.yaml');

app.use('/api-docs', swaggerUi.serve, swaggerUi.setup(swaggerDocument));
```

### Swagger Editor

Online editor: https://editor.swagger.io/

### Code Generation

**Generate client:**
```bash
# Node.js client
npx @openapitools/openapi-generator-cli generate \
  -i openapi.yaml \
  -g javascript \
  -o ./client

# Python client
openapi-generator generate \
  -i openapi.yaml \
  -g python \
  -o ./client
```

**Generate server:**
```bash
# Express server
openapi-generator generate \
  -i openapi.yaml \
  -g nodejs-express-server \
  -o ./server
```

### Validation

```bash
# Validate OpenAPI spec
npx @apidevtools/swagger-cli validate openapi.yaml
```

---

## Best Practices

1. **Use $ref for reusability** - Don't repeat schemas
2. **Add examples** - Help developers understand
3. **Document errors** - Include all error responses
4. **Version your API** - Use /v1, /v2 in URL
5. **Describe authentication** - Clear auth instructions
6. **Use tags** - Organize endpoints into groups
7. **Add descriptions** - Explain complex fields
8. **Keep it updated** - Sync with actual API
9. **Generate from code** - Use annotations (JSDoc, decorators)
10. **Test with real data** - Ensure examples work

---

## JSDoc Generation (Node.js)

```javascript
/**
 * @swagger
 * /users:
 *   get:
 *     summary: List users
 *     tags: [Users]
 *     parameters:
 *       - in: query
 *         name: page
 *         schema:
 *           type: integer
 *     responses:
 *       200:
 *         description: Success
 *         content:
 *           application/json:
 *             schema:
 *               type: array
 *               items:
 *                 $ref: '#/components/schemas/User'
 */
app.get('/users', getUsers);

/**
 * @swagger
 * components:
 *   schemas:
 *     User:
 *       type: object
 *       properties:
 *         id:
 *           type: string
 *         email:
 *           type: string
 */
```

---

## Laravel OpenAPI Generation

```php
use OpenApi\Annotations as OA;

/**
 * @OA\Get(
 *     path="/api/users",
 *     summary="List users",
 *     tags={"Users"},
 *     @OA\Parameter(
 *         name="page",
 *         in="query",
 *         @OA\Schema(type="integer")
 *     ),
 *     @OA\Response(
 *         response=200,
 *         description="Success",
 *         @OA\JsonContent(
 *             type="array",
 *             @OA\Items(ref="#/components/schemas/User")
 *         )
 *     )
 * )
 */
public function index(Request $request)
{
    return User::paginate();
}
```

Generate spec:
```bash
php artisan l5-swagger:generate
```
