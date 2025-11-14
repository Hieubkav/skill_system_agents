# Pagination, Filtering, and Searching

Comprehensive guide to pagination strategies, filtering patterns, sorting, and search implementation.

## Table of Contents

- [Pagination Strategies](#pagination-strategies)
- [Filtering](#filtering)
- [Sorting](#sorting)
- [Searching](#searching)
- [Best Practices](#best-practices)

---

## Pagination Strategies

### Offset Pagination (Page-Based)

**Simple and user-friendly but can have performance issues with large datasets.**

```http
GET /api/v1/users?page=3&limit=20

Response:
{
  "data": [...],
  "pagination": {
    "page": 3,
    "limit": 20,
    "total": 156,
    "totalPages": 8,
    "hasNext": true,
    "hasPrev": true
  },
  "links": {
    "self": "/api/v1/users?page=3&limit=20",
    "first": "/api/v1/users?page=1&limit=20",
    "prev": "/api/v1/users?page=2&limit=20",
    "next": "/api/v1/users?page=4&limit=20",
    "last": "/api/v1/users?page=8&limit=20"
  }
}
```

**Pros:**
- Simple to understand
- Easy to implement
- Can jump to any page
- User-friendly for pagination UI

**Cons:**
- Performance degrades with large offsets
- Inconsistent results if data changes between requests
- Counting total records can be expensive

### Cursor Pagination (Recommended for Large Datasets)

**Most reliable and performant for large datasets.**

```http
GET /api/v1/users?cursor=eyJpZCI6MTIzfQ&limit=20

Response:
{
  "data": [...],
  "pagination": {
    "nextCursor": "eyJpZCI6MTQzfQ",
    "prevCursor": "eyJpZCI6MTAzfQ",
    "hasMore": true,
    "limit": 20
  },
  "links": {
    "next": "/api/v1/users?cursor=eyJpZCI6MTQzfQ&limit=20",
    "prev": "/api/v1/users?cursor=eyJpZCI6MTAzfQ&limit=20"
  }
}
```

**Cursor encoding (Base64):**
```javascript
// Encode cursor
const cursor = Buffer.from(JSON.stringify({ id: 123 })).toString('base64');

// Decode cursor
const decoded = JSON.parse(Buffer.from(cursor, 'base64').toString());
```

**Pros:**
- Consistent results even when data changes
- Performant at any position
- No offset calculation needed
- Works well with real-time data

**Cons:**
- Can't jump to specific page
- More complex to implement
- Harder to display total count

### Offset with Limit

```http
GET /api/v1/users?offset=40&limit=20

Response:
{
  "data": [...],
  "pagination": {
    "offset": 40,
    "limit": 20,
    "total": 156
  }
}
```

**Similar to page-based but uses offset directly.**

---

## Filtering

### Single Filter

```http
GET /api/v1/posts?status=published
```

### Multiple Filters (AND Logic)

```http
GET /api/v1/posts?status=published&author=john&category=tech
```

### Multiple Values (OR Logic)

```http
# Posts with tags: tech OR ai OR ml
GET /api/v1/posts?tags=tech,ai,ml
```

### Range Filters

```http
# Date range
GET /api/v1/posts?created_after=2025-01-01&created_before=2025-12-31

# Number range
GET /api/v1/products?price_min=10&price_max=100
```

### Comparison Operators

```http
# Greater than
GET /api/v1/products?price_gt=50

# Less than
GET /api/v1/products?price_lt=100

# Greater than or equal
GET /api/v1/products?price_gte=50

# Less than or equal
GET /api/v1/products?price_lte=100

# Not equal
GET /api/v1/products?status_ne=draft
```

### Complex Filters (JSON)

For complex queries, use JSON in query parameter:

```http
GET /api/v1/posts?filter={"status":"published","tags":{"$in":["tech","ai"]},"views":{"$gte":100}}
```

### Filter Patterns

**Pattern 1: Simple key-value**
```http
?status=active&role=admin
```

**Pattern 2: Field operators**
```http
?title[contains]=machine&author[starts_with]=john
```

**Pattern 3: MongoDB-style**
```http
?filter={"age":{"$gte":18,"$lte":65}}
```

---

## Sorting

### Single Field Ascending

```http
GET /api/v1/users?sort=created_at
GET /api/v1/users?sort=+created_at
```

### Single Field Descending

```http
GET /api/v1/users?sort=-created_at
```

### Multiple Fields

```http
# Sort by priority (desc), then created_at (asc)
GET /api/v1/posts?sort=-priority,created_at
```

### Alternative Sort Syntax

```http
# Using order parameter
GET /api/v1/users?sort_by=created_at&order=desc

# Using separate parameters
GET /api/v1/users?sort=name&direction=asc
```

---

## Searching

### Full-Text Search

```http
# Simple search
GET /api/v1/posts?q=machine+learning

# Search in specific fields
GET /api/v1/posts?search=machine&search_fields=title,content
```

### Field-Specific Search

```http
# Contains
GET /api/v1/posts?title[contains]=machine

# Starts with
GET /api/v1/posts?author[starts_with]=john

# Ends with
GET /api/v1/posts?email[ends_with]=@example.com

# Exact match
GET /api/v1/posts?status[equals]=published
```

### Search with Filters

```http
GET /api/v1/posts?q=machine+learning&status=published&sort=-created_at
```

---

## Combined Example

Complete query with pagination, filtering, sorting, and searching:

```http
GET /api/v1/posts?
  q=machine+learning&
  status=published&
  category=tech&
  created_after=2025-01-01&
  tags=ai,ml&
  sort=-views,created_at&
  page=2&
  limit=20

Response:
{
  "data": [
    {
      "id": 1,
      "title": "Machine Learning Basics",
      "status": "published",
      "category": "tech",
      "tags": ["ai", "ml"],
      "views": 1500,
      "createdAt": "2025-03-15T10:00:00Z"
    }
  ],
  "pagination": {
    "page": 2,
    "limit": 20,
    "total": 45,
    "totalPages": 3,
    "hasNext": true,
    "hasPrev": true
  },
  "filters": {
    "q": "machine learning",
    "status": "published",
    "category": "tech",
    "tags": ["ai", "ml"],
    "created_after": "2025-01-01"
  },
  "sort": ["-views", "created_at"]
}
```

---

## Implementation Examples

### Node.js with Express & MongoDB

```javascript
app.get('/api/v1/posts', async (req, res) => {
  const {
    page = 1,
    limit = 20,
    sort = '-created_at',
    status,
    category,
    tags,
    q
  } = req.query;

  // Build filter
  const filter = {};
  if (status) filter.status = status;
  if (category) filter.category = category;
  if (tags) filter.tags = { $in: tags.split(',') };
  if (q) filter.$text = { $search: q };

  // Build sort
  const sortObj = {};
  sort.split(',').forEach(field => {
    if (field.startsWith('-')) {
      sortObj[field.slice(1)] = -1;
    } else {
      sortObj[field] = 1;
    }
  });

  // Execute query
  const skip = (page - 1) * limit;
  const [data, total] = await Promise.all([
    Post.find(filter)
      .sort(sortObj)
      .skip(skip)
      .limit(parseInt(limit)),
    Post.countDocuments(filter)
  ]);

  res.json({
    data,
    pagination: {
      page: parseInt(page),
      limit: parseInt(limit),
      total,
      totalPages: Math.ceil(total / limit),
      hasNext: page * limit < total,
      hasPrev: page > 1
    }
  });
});
```

### Laravel with Eloquent

```php
public function index(Request $request)
{
    $query = Post::query();

    // Filtering
    if ($request->has('status')) {
        $query->where('status', $request->status);
    }
    if ($request->has('category')) {
        $query->where('category', $request->category);
    }
    if ($request->has('tags')) {
        $tags = explode(',', $request->tags);
        $query->whereIn('tags', $tags);
    }

    // Searching
    if ($request->has('q')) {
        $query->where(function($q) use ($request) {
            $q->where('title', 'LIKE', "%{$request->q}%")
              ->orWhere('content', 'LIKE', "%{$request->q}%");
        });
    }

    // Sorting
    $sortField = ltrim($request->get('sort', '-created_at'), '-');
    $sortDirection = str_starts_with($request->get('sort', '-created_at'), '-') 
        ? 'desc' 
        : 'asc';
    $query->orderBy($sortField, $sortDirection);

    // Pagination
    $limit = $request->get('limit', 20);
    $posts = $query->paginate($limit);

    return response()->json([
        'data' => $posts->items(),
        'pagination' => [
            'page' => $posts->currentPage(),
            'limit' => $posts->perPage(),
            'total' => $posts->total(),
            'totalPages' => $posts->lastPage(),
            'hasNext' => $posts->hasMorePages(),
            'hasPrev' => $posts->currentPage() > 1
        ]
    ]);
}
```

### Python with Django REST Framework

```python
from rest_framework.pagination import PageNumberPagination
from rest_framework.filters import SearchFilter, OrderingFilter
from django_filters.rest_framework import DjangoFilterBackend

class PostPagination(PageNumberPagination):
    page_size = 20
    page_size_query_param = 'limit'
    max_page_size = 100

class PostViewSet(viewsets.ModelViewSet):
    queryset = Post.objects.all()
    serializer_class = PostSerializer
    pagination_class = PostPagination
    filter_backends = [DjangoFilterBackend, SearchFilter, OrderingFilter]
    
    # Filtering
    filterset_fields = ['status', 'category', 'author']
    
    # Searching
    search_fields = ['title', 'content']
    
    # Sorting
    ordering_fields = ['created_at', 'views', 'title']
    ordering = ['-created_at']

# Usage:
# GET /api/posts?status=published&search=machine&ordering=-views&page=2&limit=20
```

---

## Best Practices

### 1. Set Reasonable Limits

```javascript
const DEFAULT_LIMIT = 20;
const MAX_LIMIT = 100;

const limit = Math.min(
  parseInt(req.query.limit) || DEFAULT_LIMIT,
  MAX_LIMIT
);
```

### 2. Validate Sort Fields

```javascript
const ALLOWED_SORT_FIELDS = ['created_at', 'views', 'title'];

const validateSort = (sort) => {
  const field = sort.replace(/^-/, '');
  if (!ALLOWED_SORT_FIELDS.includes(field)) {
    throw new Error(`Invalid sort field: ${field}`);
  }
  return sort;
};
```

### 3. Index Database Fields

```javascript
// MongoDB
PostSchema.index({ status: 1, created_at: -1 });
PostSchema.index({ category: 1 });
PostSchema.index({ tags: 1 });
PostSchema.index({ title: 'text', content: 'text' });
```

### 4. Return Filter Metadata

Help clients understand what filters are applied:

```json
{
  "data": [...],
  "pagination": {...},
  "filters": {
    "status": "published",
    "category": "tech",
    "tags": ["ai", "ml"]
  },
  "sort": ["-views", "created_at"]
}
```

### 5. Use Cursor for Infinite Scroll

For mobile apps and infinite scroll:

```http
GET /api/v1/posts?cursor=xyz&limit=20
```

### 6. Cache Count Queries

Counting total records can be expensive:

```javascript
// Cache total count for 5 minutes
const totalKey = `posts:count:${filterHash}`;
let total = await redis.get(totalKey);

if (!total) {
  total = await Post.countDocuments(filter);
  await redis.set(totalKey, total, 'EX', 300);
}
```

### 7. Support Multiple Filter Formats

```http
# Simple
?status=published

# Array
?tags[]=tech&tags[]=ai

# Comma-separated
?tags=tech,ai

# JSON
?filter={"tags":{"$in":["tech","ai"]}}
```

---

## Common Pitfalls

- ❌ No pagination limits (returning all records)
- ❌ Allowing arbitrary sort fields (SQL injection risk)
- ❌ Not indexing filtered/sorted fields
- ❌ Counting total on every request
- ❌ Using offset pagination for real-time data
- ❌ Not validating limit parameter
- ❌ Exposing internal field names
- ❌ Complex filters without documentation

---

## GraphQL Pagination

### Relay Connection Spec

```graphql
type Query {
  posts(
    first: Int
    after: String
    last: Int
    before: String
    filter: PostFilter
  ): PostConnection!
}

type PostConnection {
  edges: [PostEdge!]!
  pageInfo: PageInfo!
  totalCount: Int
}

type PostEdge {
  node: Post!
  cursor: String!
}

type PageInfo {
  hasNextPage: Boolean!
  hasPreviousPage: Boolean!
  startCursor: String
  endCursor: String
}
```

---

## Testing

```javascript
describe('Pagination', () => {
  test('should return default limit', async () => {
    const res = await request(app).get('/api/posts');
    expect(res.body.data).toHaveLength(20);
  });

  test('should respect limit parameter', async () => {
    const res = await request(app).get('/api/posts?limit=10');
    expect(res.body.data).toHaveLength(10);
  });

  test('should filter by status', async () => {
    const res = await request(app).get('/api/posts?status=published');
    expect(res.body.data.every(p => p.status === 'published')).toBe(true);
  });

  test('should sort descending', async () => {
    const res = await request(app).get('/api/posts?sort=-views');
    const views = res.body.data.map(p => p.views);
    expect(views).toEqual([...views].sort((a, b) => b - a));
  });
});
```
