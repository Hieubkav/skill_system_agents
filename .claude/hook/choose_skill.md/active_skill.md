# Chọn Skill dựa theo lời gọi

## Skill ux-designer: 
- Đường dẫn: \.claude\skills\frontend\ux-designer
- Mô tả: Expert UI/UX design guidance for building unique, accessible, and user-centered interfaces. Use when designing interfaces, making visual design decisions, choosing colors/typography/layouts, implementing responsive design, or improving accessibility.
- Lời gọi kích hoạt:
  - "design" / "thiết kế"
  - "UI" / "UX"
  - "styling" / "visual"
  - "appearance" / "giao diện"
  - "colors" / "màu sắc"
  - "fonts" / "typography"
  - "layouts" / "spacing"
  - "responsive" / "mobile"
  - "accessibility" / "WCAG"
  - "user interface" / "user experience"
  - "thiết kế giao diện" / "thiết kế UI/UX"

## Skill create-skill:
- Đường dẫn: \.claude\skills\meta\create-skill
- Mô tả: Tạo Agent Skills mới cho AI AGENT theo đúng chuẩn và best practices. Dùng khi người dùng muốn tạo, viết, thiết kế skill mới, hoặc cần trợ giúp với file SKILL.md, frontmatter, hoặc cấu trúc skill. Trigger words: tạo skill, viết skill, create skill, new skill, SKILL.md, skill structure.
- Lời gọi kích hoạt:
  - "tạo skill"
  - "viết skill"
  - "create skill"
  - "new skill"
  - "skill mới"
  - "SKILL.md"
  - "skill structure"
  - "cấu trúc skill"
  - "design skill"
  - "thiết kế skill"
  - "author skill"
  - "write skill"
  - "frontmatter"
  - "Agent Skills"
  - "tạo Agent Skill"

## Skill api-cache-invalidation:
- Đường dẫn: \.claude\skills\api\api-cache-invalidation
- Mô tả: Automatic cache invalidation system với Laravel Observers và Next.js On-Demand Revalidation. Tự động sync data real-time giữa backend và frontend khi admin update. USE WHEN cần setup cache management, sync frontend-backend, API cache strategy, hoặc user phàn nàn "phải Ctrl+F5 mới thấy data mới".
- Lời gọi kích hoạt:
  - "cache invalidation"
  - "cache không update"
  - "revalidation"
  - "cache management"
  - "sync data real-time"
  - "Ctrl+F5"
  - "phải Ctrl+F5 mới thấy data mới"
  - "cache strategy"
  - "ISR"
  - "Incremental Static Regeneration"
  - "Observer pattern"
  - "Laravel Observers"
  - "Next.js revalidation"
  - "on-demand revalidation"
  - "frontend không reflect backend"

## Skill api-design-patterns:
- Đường dẫn: \.claude\skills\api\api-design-patterns
- Mô tả: Comprehensive REST and GraphQL API design patterns, best practices, OpenAPI specifications, versioning, authentication, error handling, pagination, rate limiting, and security. Use when designing APIs, creating endpoints, reviewing specifications, implementing authentication, or building scalable backend services.
- Lời gọi kích hoạt:
  - "API design"
  - "thiết kế API"
  - "REST API"
  - "GraphQL"
  - "API patterns"
  - "API best practices"
  - "OpenAPI"
  - "Swagger"
  - "API versioning"
  - "API authentication"
  - "JWT"
  - "OAuth"
  - "error handling"
  - "API pagination"
  - "rate limiting"
  - "API security"
  - "endpoint design"
  - "API specs"
  - "API review"
  - "backend API"
  - "scalable API"
  - "HTTP methods"
  - "status codes"
  - "webhooks"
  - "async operations"

## Skill api-documentation-writer:
- Đường dẫn: \.claude\skills\api\api-documentation-writer
- Mô tả: Generate comprehensive API documentation for REST, GraphQL, WebSocket APIs with OpenAPI specs, endpoint descriptions, request/response examples, error codes, authentication guides, and SDKs. USE WHEN user says 'viết document API', 'tạo API docs', 'generate API documentation', 'document REST endpoints', hoặc cần tạo technical reference cho developers.
- Lời gọi kích hoạt:
  - "viết document API"
  - "tạo API docs"
  - "generate API documentation"
  - "document REST endpoints"
  - "API documentation"
  - "document API"
  - "API docs"
  - "REST documentation"
  - "GraphQL documentation"
  - "WebSocket docs"
  - "OpenAPI spec"
  - "Swagger documentation"
  - "endpoint documentation"
  - "developer guide"
  - "technical reference"
  - "authentication guide"
  - "webhook documentation"
  - "SDK documentation"

## Skill database-schema-diff:
- Đường dẫn: \.claude\skills\database\database-schema-diff
- Mô tả: Leverages database-diff-tool plugin to compare schemas, generate migration scripts, and provide rollback procedures for PostgreSQL/MySQL. Use when comparing database schemas across environments, generating migration scripts, synchronizing databases, or creating rollback procedures. Activates on: "database diff", "schema comparison", "generate migration", "database synchronization", "/db-diff".
- Lời gọi kích hoạt:
  - "database diff"
  - "schema comparison"
  - "so sánh schema"
  - "generate migration"
  - "tạo migration"
  - "database synchronization"
  - "đồng bộ database"
  - "sync database"
  - "/db-diff"
  - "migration script"
  - "rollback procedure"
  - "compare schemas"

## Skill database-seeding:
- Đường dẫn: \.claude\skills\database\database-seeding
- Mô tả: Generate realistic database seed data and test fixtures for development, testing, and demos. Creates users, products, orders, and custom schemas with Faker libraries while maintaining relational integrity. Use when populating databases, creating test fixtures, seeding development environments, generating demo data, or creating realistic test datasets.
- Lời gọi kích hoạt:
  - "seed data"
  - "tạo seed data"
  - "database seeding"
  - "test fixtures"
  - "generate test data"
  - "populate database"
  - "fake data"
  - "sample data"
  - "demo data"
  - "Faker"
  - "test dataset"
  - "seeder"

## Skill database-performance-tuning:
- Đường dẫn: \.claude\skills\database\database-performance-tuning
- Mô tả: Analyze and optimize database performance through index analysis, query profiling, and EXPLAIN plan interpretation. Identify missing/unused indexes, find bottlenecks, and recommend optimization strategies. Use when optimizing slow queries, analyzing workloads, improving query execution speed, or managing database indexes.
- Lời gọi kích hoạt:
  - "optimize database"
  - "tối ưu database"
  - "slow query"
  - "query chậm"
  - "database performance"
  - "hiệu năng database"
  - "index optimization"
  - "EXPLAIN plan"
  - "query profiling"
  - "missing index"
  - "unused index"
  - "database tuning"

## Skill database-security-audit:
- Đường dẫn: \.claude\skills\database\database-security-audit
- Mô tả: Comprehensive database security scanning and data integrity validation. Identify vulnerabilities, enforce OWASP compliance, validate data types/formats/ranges, ensure referential integrity, and implement business rules. Use when assessing database security, checking compliance, validating data integrity, or enforcing constraints.
- Lời gọi kích hoạt:
  - "database security"
  - "bảo mật database"
  - "security scan"
  - "OWASP compliance"
  - "database audit"
  - "kiểm tra bảo mật"
  - "data validation"
  - "validate data"
  - "security vulnerability"
  - "database constraints"
  - "data integrity"
  - "referential integrity"

## Skill database-essentials:
- Đường dẫn: \.claude\skills\database\database-essentials
- Mô tả: Work with MongoDB (document database, BSON, aggregation pipelines, Atlas) and PostgreSQL (relational database, SQL, psql, pgAdmin). Use when designing schemas, writing queries/aggregations, optimizing indexes, performing migrations, configuring replication/sharding, implementing backups, managing users/permissions, analyzing performance, or administering databases.
- Lời gọi kích hoạt:
  - "MongoDB"
  - "PostgreSQL"
  - "postgres"
  - "database basics"
  - "cơ bản database"
  - "CRUD operations"
  - "database setup"
  - "database connection"
  - "aggregation pipeline"
  - "SQL query"
  - "psql"
  - "mongosh"
  - "database guide"

## Skill database-schema-design:
- Đường dẫn: \.claude\skills\database\database-schema-design
- Mô tả: Design, visualize, and document database schemas with ERD generation, normalization guidance (1NF-BCNF), relationship mapping, and automated documentation. Create efficient structures, generate SQL statements, produce interactive HTML docs, and maintain data dictionaries. Use for schema design, database modeling, ERD diagrams, normalization, and documentation.
- Lời gọi kích hoạt:
  - "design schema"
  - "thiết kế schema"
  - "ERD diagram"
  - "database design"
  - "thiết kế database"
  - "normalization"
  - "chuẩn hóa database"
  - "database modeling"
  - "entity relationship"
  - "generate ERD"
  - "data dictionary"
  - "database documentation"

## Skill orm-code-generator:
- Đường dẫn: \.claude\skills\database\orm-code-generator
- Mô tả: Generate ORM models and database schemas for TypeORM, Prisma, Sequelize, SQLAlchemy, Django ORM, Entity Framework, and Hibernate. Supports both database-to-code and code-to-database generation with entities, relationships, and validations. Use when creating ORM models, generating entities, creating migrations, or working with any ORM framework.
- Lời gọi kích hoạt:
  - "ORM model"
  - "tạo ORM"
  - "generate entity"
  - "TypeORM"
  - "Prisma"
  - "Sequelize"
  - "SQLAlchemy"
  - "Django ORM"
  - "Entity Framework"
  - "Hibernate"
  - "ORM code"
  - "generate model"

## Skill sql-query-optimization:
- Đường dẫn: \.claude\skills\database\sql-query-optimization
- Mô tả: Master SQL query optimization, indexing strategies, and EXPLAIN analysis to dramatically improve database performance and eliminate slow queries. Learn proven patterns for fixing N+1 queries, implementing efficient pagination, and optimizing JOINs. Use when debugging slow queries, designing schemas, or optimizing application performance.
- Lời gọi kích hoạt:
  - "SQL optimization"
  - "tối ưu SQL"
  - "optimize query"
  - "tối ưu query"
  - "slow SQL"
  - "SQL performance"
  - "N+1 query"
  - "query optimization"
  - "indexing strategy"
  - "EXPLAIN ANALYZE"
  - "query patterns"
  - "efficient pagination"
 
