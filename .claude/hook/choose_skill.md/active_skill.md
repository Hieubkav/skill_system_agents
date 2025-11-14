# Active Skills

- **create-skill**: Tạo Agent Skills mới theo chuẩn với SKILL.md, frontmatter, cấu trúc thư mục. Dùng khi tạo/viết/thiết kế skill mới. `\.claude\skills\meta\create-skill` | Triggers: "tạo skill", "create skill", "SKILL.md"

- **api-cache-invalidation**: Auto cache invalidation với Laravel Observers và Next.js revalidation. Sync data real-time giữa backend-frontend khi admin update. `\.claude\skills\api\api-cache-invalidation` | Triggers: "cache invalidation", "revalidation", "Ctrl+F5"

- **api-design-patterns**: REST/GraphQL API design patterns, OpenAPI specs, versioning, authentication, error handling, pagination, rate limiting. Dùng khi thiết kế APIs, tạo endpoints, review specs. `\.claude\skills\api\api-design-patterns` | Triggers: "API design", "REST API", "GraphQL"

- **api-documentation-writer**: Generate API documentation cho REST/GraphQL/WebSocket với OpenAPI specs, examples, error codes, authentication guides. `\.claude\skills\api\api-documentation-writer` | Triggers: "viết document API", "API docs", "OpenAPI"

- **api-contracts-generator**: Generate API contracts đồng bộ giữa Frontend (Next.js) và Backend (NestJS) với types, validation, error handling. `\.claude\skills\api\api-contracts-generator` | Triggers: "API contract", "DTO", "frontend-backend sync"

- **database-schema-diff**: So sánh database schemas, generate migration scripts, rollback procedures cho PostgreSQL/MySQL. `\.claude\skills\database\database-schema-diff` | Triggers: "database diff", "schema comparison", "generate migration"

- **database-seeding**: Generate realistic seed data và test fixtures với Faker libraries, maintain relational integrity. `\.claude\skills\database\database-seeding` | Triggers: "seed data", "test fixtures", "populate database"

- **database-security-audit**: Database security scanning, data integrity validation, OWASP compliance, enforce constraints. `\.claude\skills\database\database-security-audit` | Triggers: "database security", "security scan", "data validation"

- **database-essentials**: MongoDB (document DB, aggregation) và PostgreSQL (SQL, psql). Schema design, queries, optimization, migrations, backups. `\.claude\skills\database\database-essentials` | Triggers: "MongoDB", "PostgreSQL", "database basics"

- **database-schema-design**: Thiết kế database schemas với ERD generation, normalization (1NF-BCNF), relationship mapping, documentation. `\.claude\skills\database\database-schema-design` | Triggers: "design schema", "ERD diagram", "normalization"

- **database-backup**: Safe database migration workflow với Spatie backup. Backup trước migration, max 10 backups. `\.claude\skills\database\database-backup` | Triggers: "database backup", "migration backup", "restore database"

- **database-performance-optimization**: Database và SQL query optimization với index analysis, EXPLAIN plans, N+1 fixes, efficient pagination. `\.claude\skills\database\database-performance-optimization` | Triggers: "optimize database", "slow query", "index optimization"

- **discover-database**: Auto discover database skills khi làm việc với SQL, PostgreSQL, MongoDB, Redis, schema design, query optimization. `\.claude\skills\database\discover-database` | Triggers: "database", "schema", "optimization"

- **orm-code-generator**: Generate ORM models cho TypeORM, Prisma, Sequelize, SQLAlchemy, Django, Entity Framework, Hibernate. `\.claude\skills\database\orm-code-generator` | Triggers: "ORM model", "TypeORM", "Prisma"

- **filament-form-debugger**: Debug Filament 4.x form errors - namespace issues, type mismatch, trait errors. `\.claude\skills\filament\filament-form-debugger` | Triggers: "Class not found", "Filament errors", "namespace errors"

- **filament-resource-generator**: Automated Filament 4.x resource generation với Schema namespace, Vietnamese labels, Observer patterns, ImagesRelationManager. `\.claude\skills\filament\filament-resource-generator` | Triggers: "tạo resource", "Filament resource", "admin panel"

- **filament-widgets-infolists**: Filament 4.x Widgets và Infolists - dashboards, stats widgets, chart widgets, read-only infolists. `\.claude\skills\filament\filament-widgets-infolists` | Triggers: "widget", "dashboard", "stats widget"

- **image-management**: Polymorphic image management với CheckboxList picker, WebP auto-conversion, order management, soft deletes, bulk operations. `\.claude\skills\filament\image-management` | Triggers: "image management", "gallery", "upload ảnh"

- **auth-implementation-patterns**: Authentication và authorization patterns với JWT, OAuth2, session management, RBAC. Dùng khi implement auth systems. `\.claude\skills\auth\auth-implementation-patterns` | Triggers: "authentication", "authorization", "JWT"

- **better-auth**: Production-ready authentication cho TypeScript với Cloudflare D1 (Drizzle/Kysely). Thay thế tự host cho Clerk hay Auth.js. `\.claude\skills\auth\better-auth` | Triggers: "better-auth", "D1 authentication", "self-hosted auth", "Cloudflare auth", "alternative to Clerk", "OAuth với Cloudflare", "2FA authentication", "passkeys", "magic links"

- **backend-dev-guidelines**: Node.js/Express/TypeScript microservices. Layered architecture (routes→controllers→services→repositories), Prisma, Zod validation. `\.claude\skills\backend\backend-dev-guidelines` | Triggers: "backend development", "Node.js", "microservices"

- **fastapi-templates**: Production-ready FastAPI projects với async patterns, dependency injection, error handling. `\.claude\skills\backend\fastapi-templates` | Triggers: "FastAPI", "Python backend", "async API"

- **email-service-integration**: Integrate email services với SMTP, third-party providers, templates, async sending. `\.claude\skills\backend\email-service-integration` | Triggers: "email", "SMTP", "transactional email"

- **code-review-excellence**: Code review practices, constructive feedback, catch bugs early, foster knowledge sharing. `\.claude\skills\code_review\code-review-excellence` | Triggers: "code review", "pull request", "PR review"

- **brainstorming**: Refine rough ideas thành fully-formed designs qua collaborative questioning, alternative exploration. Dùng trước khi code. `\.claude\skills\critical_thinking\brainstorming` | Triggers: "brainstorm", "idea development", "design thinking"

- **sequential-thinking**: Systematic step-by-step reasoning cho complex problems với ability to revise thoughts, branch alternatives. `\.claude\skills\critical_thinking\sequential-thinking` | Triggers: "sequential thinking", "problem decomposition", "systematic analysis"

- **systematic-debugging**: Four-phase debugging framework đảm bảo root cause investigation trước khi fix. Never jump to solutions. `\.claude\skills\debug\systematic-debugging` | Triggers: "debugging", "bug fixing", "root cause"

- **chrome-devtools**: Browser automation với Puppeteer - screenshots, performance analysis, network monitoring, web scraping, form automation. `\.claude\skills\debug\chrome-devtools` | Triggers: "browser automation", "Puppeteer", "screenshots"

- **root-cause-tracing**: Trace bugs backward qua call stack để tìm original trigger. `\.claude\skills\debug\root-cause-tracing` | Triggers: "root cause", "trace bugs", "call stack"

- **docs-seeker**: Search technical documentation với llms.txt standard, GitHub repositories via Repomix, parallel exploration. `\.claude\skills\documents\docs-seeker` | Triggers: "find documentation", "llms.txt", "search docs"

- **writing-plans**: Tạo detailed implementation plans cho engineers với zero codebase context - exact file paths, complete code examples, verification steps. `\.claude\skills\documents\writing-plans` | Triggers: "implementation plan", "task breakdown", "engineer guide"

- **laravel**: Laravel v12 - PHP Framework For Web Artisans. Eloquent models, authentication, migrations, seeders, middleware, queues, cache, validation. `\.claude\skills\framework\laravel` | Triggers: "Laravel", "Eloquent", "Artisan"

- **laravel-dusk**: Laravel Dusk browser automation và testing tool. E2E testing, browser automation, user interaction testing. `\.claude\skills\framework\laravel-dusk` | Triggers: "Laravel Dusk", "browser automation Laravel", "E2E testing Laravel"

- **laravel-prompts**: Laravel Prompts - Beautiful CLI forms với browser-like features, placeholder text, validation. `\.claude\skills\framework\laravel-prompts` | Triggers: "Laravel Prompts", "CLI prompts", "Artisan prompts"

- **nextjs**: Next.js App Router patterns, Server Components, Server Actions, Cache Components. Covers Next.js 16 breaking changes, React 19.2. `\.claude\skills\framework\nextjs` | Triggers: "Next.js", "App Router", "Server Components"

- **framer-expert**: Framer design và development - interactive prototypes tới production sites với Framer Motion, CMS integration. `\.claude\skills\framework\framer-expert` | Triggers: "Framer", "Framer Motion", "interactive prototypes"

- **state-management-patterns**: State management solutions cho React - Redux Toolkit, Zustand, Context API, MobX, Recoil, React Query. `\.claude\skills\framework\state-management-patterns` | Triggers: "state management", "Redux", "Zustand"

- **tanstack-query**: TanStack Query (React Query) - queries, mutations, caching, invalidation, optimistic updates, pagination. `\.claude\skills\framework\tanstack-query` | Triggers: "TanStack Query", "React Query", "server state"

- **frontend-dev-guidelines**: Frontend development guidelines - component patterns, file organization, data fetching, loading/error states, performance, routing. `\.claude\skills\frontend\frontend-dev-guidelines` | Triggers: "frontend guidelines", "frontend best practices", "React guidelines"

- **frontend-responsive**: Responsive mobile-first layouts với fluid containers, flexible units, media queries, touch-friendly design. `\.claude\skills\frontend\frontend-responsive` | Triggers: "responsive design", "mobile-first", "media queries"

- **frontend-design**: Tạo distinctive production-grade frontend interfaces với high design quality. Avoids generic AI aesthetics. `\.claude\skills\frontend\frontend-design` | Triggers: "frontend design", "build web components", "creative design"

- **frontend-engineer**: Expert frontend engineering với simplified architecture, React 19, TanStack ecosystem, Zustand. Gateway Pattern implementation. `\.claude\skills\frontend\frontend-engineer` | Triggers: "frontend engineer", "frontend architecture", "Gateway Pattern"

- **react-component-architecture**: Design scalable React components theo single responsibility principle với functional components, hooks, composition patterns, TypeScript. `\.claude\skills\frontend\react-component-architecture` | Triggers: "React architecture", "React components", "component design"

- **ui-ux-design-system**: Expert UI/UX design toolkit. Build accessible interfaces với design tokens, component documentation, responsive calculations. `\.claude\skills\frontend\ui-ux-design-system` | Triggers: "design", "UI/UX", "design system"

- **ui-styling**: Tạo beautiful accessible interfaces với shadcn/ui (Radix UI + Tailwind), responsive design, dark mode, component patterns. `\.claude\skills\frontend\ui-styling` | Triggers: "shadcn/ui", "Tailwind CSS", "UI styling"

- **ui-component-workflow**: Generate production-ready React components với TypeScript, TailwindCSS, Framer Motion, Radix UI. WCAG 2.1 AA compliance. `\.claude\skills\frontend\ui-component-workflow` | Triggers: "UI component", "React component", "accessibility"

- **zustand-state-management**: Production-tested Zustand state management cho React với TypeScript, persist middleware, devtools, slices pattern. `\.claude\skills\frontend\zustand-state-management` | Triggers: "Zustand", "state management", "React state"

- **cache-optimization**: Analyze và improve caching strategies. Optimize cache hit rates, TTL configs, cache key design, invalidation strategies. `\.claude\skills\optimize\cache-optimization` | Triggers: "cache optimization", "Redis optimization", "cache strategy"

- **caching-strategy**: Implement efficient caching với Redis, Memcached, CDN, cache invalidation patterns. Optimize performance, reduce database load. `\.claude\skills\optimize\caching-strategy` | Triggers: "caching", "Redis", "CDN"

- **web-performance-audit**: Comprehensive web performance audits. Measure page speed, identify bottlenecks, Core Web Vitals optimization. `\.claude\skills\optimize\web-performance-audit` | Triggers: "performance audit", "Core Web Vitals", "page speed"

- **seo-optimization**: Comprehensive SEO optimization theo Google guidelines. Keyword analysis, readability scoring, meta descriptions, Search Console, structured data. `\.claude\skills\content_marketing\seo-optimization` | Triggers: "SEO optimization", "keyword analysis", "Google SEO"

- **seo-expert**: Elite SEO expertise - technical optimization, Core Web Vitals, E-E-A-T, topic clusters, entity-based SEO, AI/ML integration. `\.claude\skills\optimize\seo-expert` | Triggers: "SEO expert", "SEO strategy", "technical SEO"

- **product-search-scoring**: Advanced product search với keyword scoring, Vietnamese text normalization, multi-field matching, search ranking. `\.claude\skills\content_marketing\product-search-scoring` | Triggers: "product search", "search scoring", "search ranking"

- **playwright-e2e-testing**: Complete E2E testing với Playwright. Build test suites, automate browser tasks, test pages/forms, debug flaky tests. `\.claude\skills\testing\playwright-e2e-testing` | Triggers: "E2E testing", "Playwright", "browser automation"

- **qa-verification**: Comprehensive truth scoring, code quality verification, automatic rollback với 0.95 accuracy threshold. `\.claude\skills\testing\qa-verification` | Triggers: "QA verification", "quality assurance", "truth scoring"

- **verification-before-completion**: Run verification commands và confirm output trước khi claim success. `\.claude\skills\testing\verification-before-completion` | Triggers: "verification", "confirm output", "testing"

- **git-commit-helper**: Generate descriptive commit messages by analyzing git diffs. Dùng khi cần help writing commit messages. `\.claude\skills\common\git-commit-helper` | Triggers: "commit message", "git commit", "conventional commits"

- **repomix**: Package entire code repositories thành single AI-friendly files với Repomix. Customizable patterns, preserve file structure. `\.claude\skills\common\repomix` | Triggers: "Repomix", "package repository", "pack codebase"

- **clean-architecture**: Clean Architecture principles với layered architecture, dependency rule, domain-driven design patterns. Dùng khi organize backend layers. `\.claude\skills\architecture\clean-architecture` | Triggers: "clean architecture", "layered architecture", "DDD"

- **defense-in-depth**: Validate at every layer data passes through để make bugs impossible. Multi-layer security validation. `\.claude\skills\security\defense-in-depth` | Triggers: "defense in depth", "validation", "security"

- **convex-core**: Triển khai backend Convex đầy đủ: queries/mutations/actions, schema design với validators, HTTP endpoints, authentication/authorization, file storage, React hooks (useQuery/useMutation), environment variables, performance optimization, indexing strategies, error handling, retry logic, testing patterns. Chi tiết: queries (pagination, indexing), mutations (batch operations, transactions), actions (scheduling, external APIs), schema (validators, relationships), react (hooks, type-safety), config (secrets), files (upload/download), auth (JWT, RBAC), performance (query tuning), errors (recovery patterns), testing (unit/integration). Sử dụng khi làm việc với Convex backend, database operations, hoặc frontend integration. `\.claude\skills\convex\convex-core` | Triggers: "Convex", "Convex query", "Convex mutation", "Convex action", "Convex database", "Convex schema", "Convex React", "Convex auth", "Convex function", "backend Convex"

- **convex-agents**: Toàn bộ Convex Agents functionality: AI agents setup, context management, messages, human-in-the-loop workflows, file processing, RAG, streaming, debugging, tools, rate limiting, usage tracking, playground testing, thread management, và comprehensive agent orchestration. Sử dụng khi xây dựng AI chat agents, automation workflows, hoặc AI-powered applications. `\.claude\skills\convex\convex-agents` | Triggers: "Convex agent", "Convex AI", "Convex chat", "Convex LLM", "Convex RAG", "Convex streaming", "Convex workflow", "Convex tool"
