# GraphQL API Design Patterns

Comprehensive guide to GraphQL schema design, queries, mutations, subscriptions, and best practices.

## Table of Contents

- [Schema-First Design](#schema-first-design)
- [Core Concepts](#core-concepts)
- [Best Practices](#best-practices)
- [Schema Evolution](#schema-evolution)
- [Performance Optimization](#performance-optimization)

---

## Schema-First Design

**Core Concepts:**
- **Types** define your domain model
- **Queries** for reading data
- **Mutations** for modifying data
- **Subscriptions** for real-time updates

**Key Benefits:**
- Clients request exactly what they need (no over-fetching)
- Single endpoint, multiple operations
- Strongly typed schema with built-in validation
- Built-in introspection and documentation

---

## Core Concepts

### Types

```graphql
type User {
  id: ID!
  email: String!
  name: String!
  age: Int
  posts: [Post!]!
  createdAt: DateTime!
}

type Post {
  id: ID!
  title: String!
  content: String!
  author: User!
  published: Boolean!
}
```

### Queries

```graphql
type Query {
  # Get single user
  user(id: ID!): User
  
  # List users with pagination
  users(
    limit: Int = 10
    offset: Int = 0
    filter: UserFilter
  ): [User!]!
  
  # Search users
  searchUsers(query: String!): [User!]!
}
```

### Mutations

```graphql
type Mutation {
  # Create user
  createUser(input: CreateUserInput!): User!
  
  # Update user
  updateUser(id: ID!, input: UpdateUserInput!): User!
  
  # Delete user
  deleteUser(id: ID!): Boolean!
}

input CreateUserInput {
  email: String!
  name: String!
  password: String!
}

input UpdateUserInput {
  email: String
  name: String
  password: String
}

input UserFilter {
  role: String
  status: String
  ageMin: Int
  ageMax: Int
}
```

### Subscriptions

```graphql
type Subscription {
  # Listen to user changes
  userUpdated(id: ID!): User!
  
  # Listen to new posts
  postCreated: Post!
}
```

---

## Best Practices

### 1. Schema First

Design your schema before implementing resolvers. This ensures clear API contracts.

```graphql
# Design complete schema
type Query {
  user(id: ID!): User
}

# Then implement resolvers
const resolvers = {
  Query: {
    user: (parent, args, context) => {
      return context.db.users.findById(args.id);
    }
  }
};
```

### 2. Use Input Types

Use input types for mutations to group related fields:

```graphql
# ✅ Good - Using input type
input CreateUserInput {
  email: String!
  name: String!
  password: String!
}

type Mutation {
  createUser(input: CreateUserInput!): User!
}

# ❌ Bad - Too many arguments
type Mutation {
  createUser(
    email: String!
    name: String!
    password: String!
  ): User!
}
```

### 3. Avoid N+1 Queries

Use DataLoaders for efficient batch fetching:

```javascript
const DataLoader = require('dataloader');

// Create a batch loading function
const batchUsers = async (ids) => {
  const users = await db.users.findByIds(ids);
  return ids.map(id => users.find(u => u.id === id));
};

// Create DataLoader instance
const userLoader = new DataLoader(batchUsers);

// Use in resolver
const resolvers = {
  Post: {
    author: (post) => userLoader.load(post.authorId)
  }
};
```

### 4. Structured Error Handling

Return errors in the payload instead of throwing:

```graphql
type UserPayload {
  user: User
  errors: [UserError!]
}

type UserError {
  field: String!
  message: String!
}

type Mutation {
  createUser(input: CreateUserInput!): UserPayload!
}
```

### 5. Pagination

Use cursor-based pagination (Relay spec):

```graphql
type Query {
  users(
    first: Int
    after: String
    last: Int
    before: String
  ): UserConnection!
}

type UserConnection {
  edges: [UserEdge!]!
  pageInfo: PageInfo!
}

type UserEdge {
  node: User!
  cursor: String!
}

type PageInfo {
  hasNextPage: Boolean!
  hasPreviousPage: Boolean!
  startCursor: String
  endCursor: String
}
```

### 6. Deprecation

Use `@deprecated` directive for gradual migration:

```graphql
type User {
  # Old field (deprecated)
  fullName: String @deprecated(reason: "Use firstName and lastName instead")
  
  # New fields
  firstName: String!
  lastName: String!
}
```

### 7. Monitoring

Track query complexity and execution time:

```javascript
const { createComplexityLimitRule } = require('graphql-validation-complexity');

const server = new ApolloServer({
  schema,
  validationRules: [
    createComplexityLimitRule(1000, {
      onCost: (cost) => console.log('Query cost:', cost)
    })
  ]
});
```

---

## Schema Evolution

### Adding Fields (Non-Breaking)

```graphql
type User {
  id: ID!
  email: String!
  name: String!
  # New optional field - safe to add
  phone: String
}
```

### Deprecating Fields (Safe)

```graphql
type User {
  # Deprecate old field
  oldField: String @deprecated(reason: "Use newField instead")
  
  # Add new field
  newField: String!
}
```

### Breaking Changes (Avoid)

```graphql
# ❌ Breaking - Removing field
type User {
  id: ID!
  # email: String!  # Removed - BREAKS clients
  name: String!
}

# ❌ Breaking - Changing field type
type User {
  id: ID!
  # Was String, now Int - BREAKS clients
  age: Int!
}

# ❌ Breaking - Making field required
type User {
  id: ID!
  # Was String, now String! - BREAKS clients
  email: String!
}
```

---

## Performance Optimization

### 1. Query Depth Limiting

```javascript
const depthLimit = require('graphql-depth-limit');

const server = new ApolloServer({
  schema,
  validationRules: [depthLimit(5)]
});
```

### 2. Query Complexity Limiting

```javascript
const { createComplexityLimitRule } = require('graphql-validation-complexity');

const server = new ApolloServer({
  schema,
  validationRules: [
    createComplexityLimitRule(1000)
  ]
});
```

### 3. Persisted Queries

```javascript
const server = new ApolloServer({
  schema,
  persistedQueries: {
    cache: new InMemoryLRUCache()
  }
});
```

### 4. Response Caching

```javascript
const { ApolloServer } = require('apollo-server');
const responseCachePlugin = require('apollo-server-plugin-response-cache');

const server = new ApolloServer({
  schema,
  plugins: [responseCachePlugin()],
  resolvers: {
    Query: {
      user: (parent, args, context, info) => {
        info.cacheControl.setCacheHint({ maxAge: 60 });
        return getUser(args.id);
      }
    }
  }
});
```

---

## Example Schema

```graphql
# Scalars
scalar DateTime
scalar Email
scalar URL

# Enums
enum UserRole {
  ADMIN
  MODERATOR
  USER
}

enum PostStatus {
  DRAFT
  PUBLISHED
  ARCHIVED
}

# Types
type User {
  id: ID!
  email: Email!
  name: String!
  role: UserRole!
  avatar: URL
  posts: [Post!]!
  createdAt: DateTime!
  updatedAt: DateTime!
}

type Post {
  id: ID!
  title: String!
  content: String!
  status: PostStatus!
  author: User!
  tags: [Tag!]!
  publishedAt: DateTime
  createdAt: DateTime!
  updatedAt: DateTime!
}

type Tag {
  id: ID!
  name: String!
  posts: [Post!]!
}

# Inputs
input CreateUserInput {
  email: Email!
  name: String!
  password: String!
  role: UserRole = USER
}

input UpdateUserInput {
  email: Email
  name: String
  password: String
  avatar: URL
}

input CreatePostInput {
  title: String!
  content: String!
  status: PostStatus = DRAFT
  tags: [ID!]
}

input PostFilter {
  status: PostStatus
  authorId: ID
  tags: [ID!]
}

# Queries
type Query {
  # Users
  user(id: ID!): User
  users(
    limit: Int = 10
    offset: Int = 0
    role: UserRole
  ): [User!]!
  
  # Posts
  post(id: ID!): Post
  posts(
    limit: Int = 10
    offset: Int = 0
    filter: PostFilter
  ): [Post!]!
  
  # Tags
  tag(id: ID!): Tag
  tags: [Tag!]!
}

# Mutations
type Mutation {
  # Users
  createUser(input: CreateUserInput!): User!
  updateUser(id: ID!, input: UpdateUserInput!): User!
  deleteUser(id: ID!): Boolean!
  
  # Posts
  createPost(input: CreatePostInput!): Post!
  updatePost(id: ID!, input: UpdatePostInput!): Post!
  deletePost(id: ID!): Boolean!
  publishPost(id: ID!): Post!
}

# Subscriptions
type Subscription {
  postCreated: Post!
  postUpdated(id: ID!): Post!
  userStatusChanged(id: ID!): User!
}
```

---

## Common Mistakes to Avoid

- ❌ N+1 query problems (not using DataLoaders)
- ❌ No depth/complexity limiting
- ❌ Generic error handling (throwing instead of returning errors)
- ❌ Over-fetching by providing too many fields
- ❌ No caching strategy
- ❌ Exposing all database fields directly
- ❌ Not using input types for mutations
- ❌ Breaking changes without deprecation
- ❌ Not monitoring query performance
- ❌ Tight coupling to database schema

---

## When to Choose GraphQL vs REST

### Choose GraphQL when:
- Complex data requirements
- Multiple client types (web, mobile, desktop)
- Need flexible queries
- Rapid frontend development
- Real-time updates (subscriptions)
- Reducing over-fetching/under-fetching

### Choose REST when:
- Simple CRUD operations
- Need HTTP caching
- Working with files/binary data
- Team familiar with REST
- Mobile apps with strict bandwidth limits
- Simpler debugging and monitoring needs
