# Chọn Skill dựa theo lời gọi


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


## Skill filament-form-debugger:
- Đường dẫn: \.claude\skills\filament\filament-form-debugger
- Mô tả: Diagnose and fix common Filament 4.x form errors - namespace issues (Tabs/Grid/Get), type mismatch, trait errors. USE WHEN encountering 'Class not found', 'Argument must be of type', namespace errors, or Filament compilation/runtime errors.
- Lời gọi kích hoạt:
  - "Class not found"
  - "Argument must be of type"
  - "Trait not found"
  - "namespace errors"
  - "lỗi namespace"
  - "Filament errors"
  - "lỗi Filament"
  - "form errors"
  - "lỗi form"
  - "Filament compilation"
  - "sửa lỗi Filament"
  - "debug Filament"
  - "Filament namespace"


## Skill image-management:
- Đường dẫn: \.claude\skills\filament\image-management
- Mô tả: Centralized polymorphic image management with CheckboxList picker, WebP auto-conversion, order management (order=0 for cover), soft deletes, bulk operations, and advanced Filament 4.x integrations. USE WHEN adding images/gallery to models, implementing image upload, working with ImagesRelationManager, bulk image actions, or fixing image errors.
- Lời gọi kích hoạt:
  - "image management"
  - "quản lý ảnh"
  - "image gallery"
  - "gallery ảnh"
  - "image upload"
  - "upload ảnh"
  - "ImagesRelationManager"
  - "polymorphic images"
  - "WebP conversion"
  - "image errors"
  - "lỗi ảnh"
  - "featured image"
  - "ảnh đại diện"
  - "bulk image"
  - "optimize images"

## Skill filament-widgets-infolists:
- Đường dẫn: \.claude\skills\filament\filament-widgets-infolists
- Mô tả: Master Filament 4.x Widgets and Infolists - create dynamic dashboards, stats widgets, chart widgets, table widgets, and read-only infolists. Includes partial rendering, custom data sources, multi-dashboard support. USE WHEN creating dashboards, stats displays, charts, read-only data views, or organizing widgets/infolists in Filament.
- Lời gọi kích hoạt:
  - "widget"
  - "widgets"
  - "dashboard"
  - "stats widget"
  - "chart widget"
  - "table widget"
  - "infolist"
  - "infolists"
  - "stats overview"
  - "dashboard widget"
  - "multi-dashboard"
  - "read-only display"
  - "data display"
  - "chart"
  - "biểu đồ"
  - "thống kê"
  - "dashboard Filament"

## Skill auth-implementation-patterns:
- Đường dẫn: \.claude\skills\auth\auth-implementation-patterns
- Mô tả: Master authentication and authorization patterns including JWT, OAuth2, session management, and RBAC to build secure, scalable access control systems. Use when implementing auth systems, securing APIs, or debugging security issues.
- Lời gọi kích hoạt:
  - "authentication"
  - "xác thực"
  - "authorization"
  - "phân quyền"
  - "JWT"
  - "OAuth2"
  - "session management"
  - "RBAC"
  - "role-based access"
  - "auth patterns"
  - "security"
  - "bảo mật"
  - "access control"
  - "SSO"
  - "multi-tenancy"

## Skill backend-dev-guidelines:
- Đường dẫn: \.claude\skills\backend\backend-dev-guidelines
- Mô tả: Node.js/Express/TypeScript microservices development. Layered architecture (routes → controllers → services → repositories), BaseController, error handling, Sentry monitoring, Prisma, Zod validation, dependency injection. USE WHEN creating routes, controllers, services, repositories, middleware, API endpoints, database access, error tracking.
- Lời gọi kích hoạt:
  - "backend development"
  - "phát triển backend"
  - "Node.js"
  - "Express"
  - "TypeScript backend"
  - "microservices"
  - "API endpoints"
  - "controllers"
  - "services"
  - "repositories"
  - "Prisma"
  - "Zod validation"
  - "error handling"
  - "Sentry"

## Skill fastapi-templates:
- Đường dẫn: \.claude\skills\backend\fastapi-templates
- Mô tả: Create production-ready FastAPI projects with async patterns, dependency injection, and comprehensive error handling. Use when building new FastAPI applications or setting up backend API projects.
- Lời gọi kích hoạt:
  - "FastAPI"
  - "Python backend"
  - "async API"
  - "FastAPI templates"
  - "FastAPI project"
  - "Python API"
  - "FastAPI setup"
  - "async patterns"
  - "dependency injection"
  - "FastAPI development"

## Skill code-review-excellence:
- Đường dẫn: \.claude\skills\code_review\code-review-excellence
- Mô tả: Master effective code review practices to provide constructive feedback, catch bugs early, and foster knowledge sharing while maintaining team morale. Use when reviewing pull requests, establishing review standards, or mentoring developers.
- Lời gọi kích hoạt:
  - "code review"
  - "review code"
  - "pull request"
  - "PR review"
  - "code feedback"
  - "review standards"
  - "mentoring"
  - "code quality"
  - "constructive feedback"
  - "review practices"

## Skill brainstorming:
- Đường dẫn: \.claude\skills\critical_thinking\brainstorming
- Mô tả: Use when creating or developing, before writing code or implementation plans - refines rough ideas into fully-formed designs through collaborative questioning, alternative exploration, and incremental validation. Don't use during clear 'mechanical' processes.
- Lời gọi kích hoạt:
  - "brainstorm"
  - "brainstorming"
  - "idea development"
  - "design thinking"
  - "explore ideas"
  - "collaborative design"
  - "refine ideas"
  - "alternative approaches"

## Skill sequential-thinking:
- Đường dẫn: \.claude\skills\critical_thinking\sequential-thinking
- Mô tả: Use when complex problems require systematic step-by-step reasoning with ability to revise thoughts, branch into alternative approaches, or dynamically adjust scope. Ideal for multi-stage analysis, design planning, problem decomposition, or tasks with initially unclear scope.
- Lời gọi kích hoạt:
  - "sequential thinking"
  - "step-by-step reasoning"
  - "problem decomposition"
  - "systematic analysis"
  - "iterative reasoning"
  - "complex problem"
  - "multi-stage analysis"

## Skill database-backup:
- Đường dẫn: \.claude\skills\database\database-backup
- Mô tả: Safe database migration workflow with Spatie backup integration. Always backup before migration, update mermaid.rb schema, keep max 10 recent backups. USE WHEN creating migrations, running migrations, restoring database, managing schema changes, or any risky database operations.
- Lời gọi kích hoạt:
  - "database backup"
  - "backup database"
  - "sao lưu database"
  - "migration backup"
  - "restore database"
  - "schema changes"
  - "database migration"
  - "Spatie backup"

## Skill systematic-debugging:
- Đường dẫn: \.claude\skills\debug\systematic-debugging
- Mô tả: Four-phase debugging framework ensuring root cause investigation before fixes. Never jump to solutions. USE WHEN encountering bugs, test failures, unexpected behavior, or when fixes fail repeatedly.
- Lời gọi kích hoạt:
  - "debugging"
  - "debug"
  - "gỡ lỗi"
  - "bug fixing"
  - "troubleshooting"
  - "root cause"
  - "systematic debugging"
  - "test failures"
  - "unexpected behavior"

## Skill docs-seeker:
- Đường dẫn: \.claude\skills\documents\docs-seeker
- Mô tả: Searching internet for technical documentation using llms.txt standard, GitHub repositories via Repomix, and parallel exploration. Use when user needs: (1) Latest documentation for libraries/frameworks, (2) Documentation in llms.txt format, (3) GitHub repository analysis, (4) Documentation without direct llms.txt support, (5) Multiple documentation sources in parallel.
- Lời gọi kích hoạt:
  - "find documentation"
  - "tìm tài liệu"
  - "search docs"
  - "llms.txt"
  - "documentation"
  - "library docs"
  - "framework docs"
  - "GitHub repository"
  - "technical docs"

## Skill writing-plans:
- Đường dẫn: \.claude\skills\documents\writing-plans
- Mô tả: Use when design is complete and you need detailed implementation tasks for engineers with zero codebase context - creates comprehensive implementation plans with exact file paths, complete code examples, and verification steps assuming engineer has minimal domain knowledge.
- Lời gọi kích hoạt:
  - "writing plans"
  - "implementation plan"
  - "kế hoạch triển khai"
  - "detailed tasks"
  - "task breakdown"
  - "engineer guide"
  - "step-by-step plan"

## Skill laravel:
- Đường dẫn: \.claude\skills\framework\laravel
- Mô tả: Laravel v12 - The PHP Framework For Web Artisans. Building Laravel applications, Eloquent models, authentication, migrations, seeders, middleware, queues, cache, validation, and best practices.
- Lời gọi kích hoạt:
  - "Laravel"
  - "Laravel 12"
  - "Eloquent"
  - "Artisan"
  - "PHP framework"
  - "Laravel app"
  - "Blade templates"
  - "Laravel migration"
  - "Laravel seeder"
  - "Laravel middleware"

## Skill laravel-dusk:
- Đường dẫn: \.claude\skills\framework\laravel-dusk
- Mô tả: Laravel Dusk - Browser automation and testing tool for Laravel applications. E2E testing, browser automation, user interaction testing.
- Lời gọi kích hoạt:
  - "Laravel Dusk"
  - "Dusk testing"
  - "browser automation Laravel"
  - "E2E testing Laravel"
  - "Laravel browser test"
  - "Dusk"

## Skill laravel-prompts:
- Đường dẫn: \.claude\skills\framework\laravel-prompts
- Mô tả: Laravel Prompts - Beautiful and user-friendly forms for command-line applications with browser-like features including placeholder text and validation.
- Lời gọi kích hoạt:
  - "Laravel Prompts"
  - "CLI prompts"
  - "Artisan prompts"
  - "command-line forms"
  - "interactive CLI"
  - "console prompts"

## Skill nextjs:
- Đường dẫn: \.claude\skills\framework\nextjs
- Mô tả: Use this skill for Next.js App Router patterns, Server Components, Server Actions, Cache Components, and framework-level optimizations. Covers Next.js 16 breaking changes including async params, proxy.ts migration, Cache Components with "use cache", and React 19.2 integration.
- Lời gọi kích hoạt:
  - "Next.js"
  - "Next.js 16"
  - "App Router"
  - "Server Components"
  - "Server Actions"
  - "use cache"
  - "Next.js metadata"
  - "generateMetadata"
  - "Next.js SEO"
  - "Route Handlers"
  - "Next.js middleware"


## Skill frontend-dev-guidelines:
- Đường dẫn: \.claude\skills\frontend\frontend-dev-guidelines
- Mô tả: Frontend development guidelines covering component patterns, file organization, data fetching, loading/error states, performance, routing, styling, and TypeScript standards for React/Next.js applications.
- Lời gọi kích hoạt:
  - "frontend guidelines"
  - "frontend best practices"
  - "frontend patterns"
  - "React guidelines"
  - "frontend standards"
  - "component patterns"
  - "frontend architecture"

## Skill frontend-responsive:
- Đường dẫn: \.claude\skills\frontend\frontend-responsive
- Mô tả: Build responsive, mobile-first layouts using fluid containers, flexible units, media queries, and touch-friendly design that works across all screen sizes.
- Lời gọi kích hoạt:
  - "responsive design"
  - "mobile-first"
  - "breakpoints"
  - "media queries"
  - "responsive layout"
  - "mobile design"
  - "touch-friendly"
  - "fluid layout"


## Skill zustand-state-management:
- Đường dẫn: \.claude\skills\frontend\zustand-state-management
- Mô tả: Production-tested setup for Zustand state management in React applications with TypeScript. Provides comprehensive patterns for building scalable, type-safe global state with persist middleware, devtools, and slices pattern.
- Lời gọi kích hoạt:
  - "Zustand"
  - "state management"
  - "React state"
  - "global state"
  - "Zustand persist"
  - "Zustand middleware"
  - "Zustand TypeScript"

## Skill cache-optimization:
- Đường dẫn: \.claude\skills\optimize\cache-optimization
- Mô tả: Analyze and improve application caching strategies. Optimize cache hit rates, TTL configurations, cache key design, and invalidation strategies for Redis and other caching systems.
- Lời gọi kích hoạt:
  - "cache optimization"
  - "optimize cache"
  - "cache performance"
  - "Redis optimization"
  - "cache strategy"
  - "cache hit rate"
  - "TTL optimization"

## Skill web-performance-audit:
- Đường dẫn: \.claude\skills\optimize\web-performance-audit
- Mô tả: Conduct comprehensive web performance audits. Measure page speed, identify bottlenecks, and recommend optimizations to improve user experience and SEO with Core Web Vitals.
- Lời gọi kích hoạt:
  - "performance audit"
  - "web performance"
  - "page speed"
  - "Core Web Vitals"
  - "LCP"
  - "FID"
  - "CLS"
  - "performance optimization"
  - "speed optimization"


## Skill product-search-scoring:
- Đường dẫn: \.claude\skills\content_marketing\product-search-scoring
- Mô tả: Advanced product search system with keyword scoring, Vietnamese text normalization, multi-field matching, and search result ranking. USE WHEN implementing search functionality, adding keyword scoring to products, optimizing search algorithm.
- Lời gọi kích hoạt:
  - "product search"
  - "tìm kiếm sản phẩm"
  - "search scoring"
  - "keyword scoring"
  - "search ranking"
  - "search algorithm"
  - "Vietnamese search"


## Skill qa-verification:
- Đường dẫn: \.claude\skills\testing\qa-verification
- Mô tả: Comprehensive truth scoring, code quality verification, and automatic rollback system with 0.95 accuracy threshold for ensuring high-quality agent outputs and codebase reliability.
- Lời gọi kích hoạt:
  - "QA verification"
  - "quality assurance"
  - "truth scoring"
  - "code verification"
  - "quality metrics"
  - "automatic rollback"
  - "verification checks"

## Skill git-commit-helper:
- Đường dẫn: \.claude\skills\common\git-commit-helper
- Mô tả: Generate descriptive commit messages by analyzing git diffs. Use when the user asks for help writing commit messages or reviewing staged changes.
- Lời gọi kích hoạt:
  - "commit message"
  - "git commit"
  - "commit helper"
  - "write commit"
  - "generate commit"
  - "conventional commits"

## Skill repomix:
- Đường dẫn: \.claude\skills\common\repomix
- Mô tả: Package entire code repositories into single AI-friendly files using Repomix. Pack codebases with customizable include/exclude patterns, generate multiple output formats, preserve file structure, optimize for AI consumption. Use when packaging codebases for AI analysis, creating repository snapshots.
- Lời gọi kích hoạt:
  - "Repomix"
  - "package repository"
  - "pack codebase"
  - "repository snapshot"
  - "AI-friendly format"
  - "code packaging"
## Skill database-performance-optimization:
- Đường dẫn: \.claude\skills\database\database-performance-optimization
- Mô tả: Master database and SQL query optimization through index analysis, EXPLAIN plans, query profiling, N+1 pattern fixes, efficient pagination, and performance tuning. Identify bottlenecks, optimize slow queries, and implement indexing strategies for dramatically improved database performance. Use when debugging slow queries, analyzing workloads, improving query execution speed, or optimizing application performance.
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
  - "SQL optimization"
  - "tối ưu SQL"
  - "optimize query"
  - "tối ưu query"
  - "SQL performance"
  - "N+1 query"
  - "query optimization"
  - "indexing strategy"
  - "EXPLAIN ANALYZE"
  - "query patterns"
  - "efficient pagination"

## Skill playwright-e2e-testing:
- Đường dẫn: \.claude\skills\testing\playwright-e2e-testing
- Mô tả: Complete E2E testing and browser automation with Playwright. Build reliable test suites, automate browser tasks, test pages/forms, validate UX, check responsive design, test login flows, implement test patterns, debug flaky tests. Auto-detects dev servers, provides testing best practices. Use when implementing E2E tests, automating browser interactions, establishing testing standards, or debugging flaky tests.
- Lời gọi kích hoạt:
  - "E2E testing"
  - "end-to-end testing"
  - "Playwright"
  - "browser automation"
  - "test automation"
  - "E2E patterns"
  - "flaky tests"
  - "testing standards"
  - "Playwright test"
  - "web automation"
  - "test browser"
  - "automated testing"

## Skill seo-optimization:
- Đường dẫn: \.claude\skills\content_marketing\seo-optimization
- Mô tả: Comprehensive SEO optimization using official Google guidelines. Optimize content with keyword analysis, readability scoring, meta descriptions, Search Console, crawling, indexing, structured data, and rich results. Use when improving SEO, optimizing content, analyzing search performance, or following Google best practices.
- Lời gọi kích hoạt:
  - "SEO optimization"
  - "optimize SEO"
  - "SEO content"
  - "keyword analysis"
  - "meta description"
  - "readability"
  - "content optimization"
  - "search optimization"
  - "Google SEO"
  - "SEO best practices"
  - "Google Search Console"
  - "search visibility"
  - "crawling"
  - "indexing"
  - "structured data"
  - "rich results"
  - "Google ranking"

## Skill ui-ux-design-system:
- Đường dẫn: \.claude\skills\frontend\ui-ux-design-system
- Mô tả: Expert UI/UX design and design system toolkit. Build accessible, user-centered interfaces with design tokens, component documentation, responsive calculations, visual design decisions (colors, typography, layouts). Use when designing interfaces, creating design systems, making design decisions, choosing colors/typography/layouts, implementing responsive design, or improving accessibility.
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
  - "design system"
  - "design tokens"
  - "design guidelines"
  - "component documentation"
  - "design tokens generation"

## Skill filament-resource-generator:
- Đường dẫn: \.claude\skills\filament\filament-resource-generator
- Mô tả: Automated Filament 4.x resource generation following coding standards (Schema namespace, Vietnamese labels, Observer patterns, ImagesRelationManager). Includes namespace rules, best practices, form/table patterns, and RelationManagers. USE WHEN creating resources, fixing namespace errors, implementing forms/tables, RelationManagers, Settings pages, or any Filament development.
- Lời gọi kích hoạt:
  - "tạo resource mới"
  - "create new resource"
  - "generate Filament resource"
  - "scaffold admin resource"
  - "tạo Filament resource"
  - "new Filament resource"
  - "admin panel resource"
  - "generate resource"
  - "make filament resource"
  - "filament resource"
  - "admin resource"
  - "tạo admin resource"
  - "Filament standards"
  - "chuẩn Filament"
  - "Filament 4.x"
  - "Filament development"
  - "Filament coding"
  - "Filament best practices"
  - "Schema namespace"
  - "RelationManager"
  - "Filament Settings"
  - "Filament form"
  - "Filament table"
  - "Filament patterns"

## Skill react-component-architecture:
- Đường dẫn: \.claude\skills\frontend\react-component-architecture
- Mô tả: Design scalable, reusable React components following single responsibility principle with functional components, hooks, composition patterns, TypeScript, clear interfaces, encapsulation, and minimal props. Use when building reusable component libraries, maintainable UI systems, or creating/modifying frontend components in any framework.
- Lời gọi kích hoạt:
  - "React architecture"
  - "React components"
  - "React hooks"
  - "React patterns"
  - "React composition"
  - "React TypeScript"
  - "React best practices"
  - "frontend components"
  - "UI components"
  - "component design"
  - "reusable components"
  - "component architecture"
  - "Vue components"

## Skill ui-styling:
- Đường dẫn: \.claude\skills\frontend\ui-styling
- Mô tả: Create beautiful, accessible user interfaces with shadcn/ui components (Radix UI + Tailwind), Tailwind CSS utility-first framework, responsive design, dark mode, component patterns, and canvas-based visual designs. Master Tailwind utilities, customization, production optimization. Use when building user interfaces, implementing design systems, creating responsive layouts, styling components, customizing themes, or establishing styling patterns.
- Lời gọi kích hoạt:
  - "shadcn/ui"
  - "shadcn"
  - "Radix UI"
  - "UI styling"
  - "accessible components"
  - "design system"
  - "Tailwind components"
  - "dark mode"
  - "Tailwind CSS"
  - "Tailwind"
  - "utility-first CSS"
  - "Tailwind dark mode"
  - "Tailwind responsive"
  - "Tailwind customization"
## Skill api-contracts-generator:
- Đường dẫn: \.claude\skills\api\api-contracts-generator
- Mô tả: Génère des contrats API cohérents entre Frontend (Next.js) et Backend (NestJS) avec types synchronisés, validation standardisée et error handling uniforme. À utiliser lors de la création d'APIs, DTOs, types frontend/backend, ou quand l'utilisateur mentionne "API", "DTO", "types", "contract", "validation", "frontend-backend", "synchronisation".
- Lời gọi kích hoạt:
  - "api"
  - "api contracts generator"
  - "backend"
  - "contract"
  - "dto"
  - "frontend"
  - "frontend-backend"
  - "synchronisation"
  - "types"
  - "validation"

## Skill backend-dev-guidelines:
- Đường dẫn: \.claude\skills\backend\backend-dev-guidelines
- Mô tả: Comprehensive backend development guide for Langfuse's Next.js 14/tRPC/Express/TypeScript monorepo. Use when creating tRPC routers, public API endpoints, BullMQ queue processors, services, or working with tRPC procedures, Next.js API routes, Prisma database access, ClickHouse analytics queries, Redis queues, OpenTelemetry instrumentation, Zod v4 validation, env.mjs configuration, tenant isolation patterns, or async patterns. Covers layered architecture (tRPC procedures → services, queue processors → services), dual database system (PostgreSQL + ClickHouse), projectId filtering for multi-tenant isolation, traceException error handling, observability patterns, and testing strategies (Jest for web, vitest for worker).
- Lời gọi kích hoạt:
  - "api"
  - "backend"
  - "backend-dev-guidelines"
  - "bullmq"
  - "clickhouse"
  - "database"
  - "express"
  - "next.js"
  - "postgresql"
  - "prisma"
  - "trpc"
  - "typescript"
  - "validation"

## Skill caching-strategy:
- Đường dẫn: \.claude\skills\optimize\caching-strategy
- Mô tả: Implement efficient caching strategies using Redis, Memcached, CDN, and cache invalidation patterns. Use when optimizing application performance, reducing database load, or improving response times.
- Lời gọi kích hoạt:
  - "cache"
  - "caching"
  - "caching-strategy"
  - "cdn"
  - "database"
  - "invalidation"
  - "memcached"
  - "optimization"
  - "patterns"
  - "performance"
  - "redis"

## Skill clean-architecture:
- Đường dẫn: \.claude\skills\architecture\clean-architecture
- Mô tả: Clean Architecture principles including layered architecture, dependency rule, and domain-driven design patterns. **ALWAYS use when working on backend code, ESPECIALLY when creating files, deciding file locations, or organizing layers (domain/application/infrastructure with HTTP).** Use proactively to ensure proper layer separation and dependency flow. Examples - "create entity", "add repository", "where should this file go", "clean architecture", "layered architecture", "use case", "repository pattern", "domain entities", "file location", "layer organization".
- Lời gọi kích hoạt:
  - "architecture"
  - "backend"
  - "clean"
  - "clean-architecture"
  - "dependency"
  - "design"
  - "domain"
  - "domain-driven"
  - "entities"
  - "layered"
  - "patterns"
  - "repository"

## Skill defense-in-depth:
- Đường dẫn: \.claude\skills\security\defense-in-depth
- Mô tả: Validate at every layer data passes through to make bugs impossible
- Lời gọi kích hoạt:
  - "bugs"
  - "defense"
  - "defense-in-depth"
  - "impossible"
  - "security"
  - "validate"
  - "validation"

## Skill discover-database:
- Đường dẫn: \.claude\skills\database\discover-database
- Mô tả: Automatically discover database skills when working with SQL, PostgreSQL, MongoDB, Redis, database schema design, query optimization, migrations, connection pooling, ORMs, or database selection. Activates for database design, optimization, and implementation tasks.
- Lời gọi kích hoạt:
  - "database"
  - "design"
  - "discover"
  - "discover-database"
  - "migration"
  - "mongodb"
  - "optimization"
  - "postgresql"
  - "redis"
  - "schema"
  - "selection"

## Skill email-service-integration:
- Đường dẫn: \.claude\skills\backend\email-service-integration
- Mô tả: Integrate email services with backends using SMTP, third-party providers, templates, and asynchronous sending. Use when implementing email functionality, sending transactional emails, and managing email workflows.
- Lời gọi kích hoạt:
  - "asynchronous"
  - "backend"
  - "email"
  - "email-service-integration"
  - "functionality"
  - "integration"
  - "sending"
  - "services"
  - "smtp"
  - "templates"
  - "transactional"
  - "workflows"

## Skill framer-expert:
- Đường dẉn: \.claude\skills\framework\framer-expert
- Mô tả: Expert in Framer design and development - from interactive prototypes to production sites with Framer Motion, CMS integration, and the Framer MCP server
- Lời gọi kích hoạt:
  - "cms"
  - "design"
  - "development"
  - "framer"
  - "framer-expert"
  - "interactive"
  - "motion"
  - "production"
  - "prototypes"
  - "server"

## Skill frontend-design:
- Đường dẫn: \.claude\skills\frontend\frontend-design
- Mô tả: Create distinctive, production-grade frontend interfaces with high design quality. Use this skill when the user asks to build web components, pages, or applications. Generates creative, polished code that avoids generic AI aesthetics.
- Lời gọi kích hoạt:
  - "applications"
  - "build"
  - "component"
  - "creative"
  - "design"
  - "distinctive"
  - "frontend"
  - "frontend-design"
  - "interfaces"
  - "pages"
  - "production"
  - "quality"

## Skill frontend-engineer:
- Đường dẫn: \.claude\skills\frontend\frontend-engineer
- Mô tả: Expert frontend engineering with simplified pragmatic architecture, React 19, TanStack ecosystem, and Zustand state management. **ALWAYS use when implementing ANY frontend features.** Use when setting up project structure, creating pages and state management, designing gateway injection patterns, setting up HTTP communication and routing, organizing feature modules, or optimizing performance. **ALWAYS use when implementing Gateway Pattern (Interface + HTTP + Fake), Context API injection, Zustand stores, TanStack Router, or feature-based architecture.**
- Lời gọi kích hoạt:
  - "api"
  - "architecture"
  - "engineering"
  - "frontend"
  - "frontend-engineer"
  - "gateway"
  - "http"
  - "injection"
  - "interface"
  - "patterns"
  - "performance"
  - "react"
  - "tanstack"
  - "zustand"

## Skill chrome-devtools:
- Đường dẫn: \.claude\skills\debug\chrome-devtools
- Mô tả: Browser automation, debugging, and performance analysis using Puppeteer CLI scripts. Use for automating browsers, taking screenshots, analyzing performance, monitoring network traffic, web scraping, form automation, and JavaScript debugging.
- Lời gọi kích hoạt:
  - "analyzing"
  - "automation"
  - "browser"
  - "chrome"
  - "chrome-devtools"
  - "debugging"
  - "devtools"
  - "javascript"
  - "monitoring"
  - "network"
  - "performance"
  - "puppeteer"
  - "screenshots"
  - "scraping"
  - "traffic"

## Skill root-cause-tracing:
- Đường dẫn: \.claude\skills\debug\root-cause-tracing
- Mô tả: Systematically trace bugs backward through call stack to find original trigger
- Lời gọi kích hoạt:
  - "backward"
  - "bugs"
  - "debugging"
  - "find"
  - "original"
  - "root"
  - "root-cause-tracing"
  - "stack"
  - "systematically"
  - "trace"
  - "tracing"
  - "trigger"

## Skill seo-expert:
- Đường dẫn: \.claude\skills\optimize\seo-expert
- Mô tả: Elite SEO expertise covering technical optimization, Core Web Vitals, E-E-A-T, topic clusters, entity-based SEO, semantic search, advanced link building, AI/ML integration, and sustainable ranking strategies for 2025. Use for comprehensive SEO audits, strategy development, content optimization, and technical implementation.
- Lời gọi kích hoạt:
  - "audits"
  - "building"
  - "clusters"
  - "content"
  - "core web vitals"
  - "entity"
  - "integration"
  - "optimization"
  - "ranking"
  - "search"
  - "semantic"
  - "seo"
  - "seo-expert"
  - "strategy"
  - "technical"

## Skill state-management-patterns:
- Đường dẫn: \.claude\skills\framework\state-management-patterns
- Mô tả: Comprehensive guide to choosing and implementing state management solutions in React applications - Redux Toolkit, Zustand, Context API, MobX, Recoil, and React Query
- Lời gọi kích hoạt:
  - "api"
  - "applications"
  - "choosing"
  - "implementing"
  - "management"
  - "mobx"
  - "patterns"
  - "query"
  - "react"
  - "recoil"
  - "redux"
  - "solutions"
  - "state"
  - "state-management-patterns"
  - "zustand"

## Skill systematic-debugging:
- Đường dẫn: \.claude\skills\debug\systematic-debugging
- Mô tả: Four-phase debugging framework that ensures root cause investigation before attempting fixes. Never jump to solutions.
- Lời gọi kích hoạt:
  - "attempting"
  - "cause"
  - "debugging"
  - "ensures"
  - "fixes"
  - "framework"
  - "investigation"
  - "never"
  - "phase"
  - "root"
  - "solutions"
  - "systematic"
  - "systematic-debugging"

## Skill tanstack-query:
- Đường dẫn: \.claude\skills\framework\tanstack-query
- Mô tả: Expert guidance for TanStack Query (React Query) including queries, mutations, caching, invalidation, optimistic updates, pagination, and infinite queries. Use this when managing server state in React applications.
- Lời gọi kích hoạt:
  - "applications"
  - "caching"
  - "infinite"
  - "invalidation"
  - "managing"
  - "mutations"
  - "optimistic"
  - "pagination"
  - "queries"
  - "query"
  - "react"
  - "server"
  - "state"
  - "tanstack"
  - "tanstack-query"

## Skill ui-component-workflow:
- Đường dẫn: \.claude\skills\frontend\ui-component-workflow
- Mô tả: Generate production-ready React components with TypeScript, TailwindCSS, Framer Motion animations, and Radix UI. Includes accessibility compliance (WCAG 2.1 AA), responsive design, and dark mode support. Use when creating new UI features or refactoring components.
- Lời gọi kích hoạt:
  - "accessibility"
  - "animations"
  - "component"
  - "components"
  - "creating"
  - "dark mode"
  - "design"
  - "features"
  - "framer"
  - "motion"
  - "production"
  - "radix"
  - "react"
  - "refactoring"
  - "responsive"
  - "tailwindcss"
  - "typescript"
  - "ui"
  - "ui-component-workflow"
  - "wcag"

## Skill verification-before-completion:
- Đường dẫn: \.claude\skills\testing\verification-before-completion
- Mô tả: Run verification commands and confirm output before claiming success
- Lời gọi kích hoạt:
  - "before"
  - "claiming"
  - "commands"
  - "completion"
  - "confirm"
  - "output"
  - "success"
  - "testing"
  - "verification"
  - "verification-before-completion"
