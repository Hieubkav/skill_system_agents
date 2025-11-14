# Skill Index (Fast Scan)

**Purpose**: Lightweight index for AI to quickly match skills. Load full details on-demand.

**Usage**:
1. Scan this index for matching keywords/triggers
2. Load full skill details from: `.claude/hook/choose_skill.md/skills/{domain}/{skill-name}.md`

**Stats**: 42 skills across 16 domains

---

## Quick Reference by Domain

### API (3 skills)

**api-cache-invalidation**  
Automatic cache invalidation system với Laravel Observers và Next.js On-Demand Revalidation  
🔍 "cache invalidation", "cache không update", "revalidation"  
📂 `\.claude\skills\api\api-cache-invalidation`

**api-design-patterns**  
Comprehensive REST and GraphQL API design patterns, best practices, OpenAPI specifications, versioning, authentication, error handling, pagination,...  
🔍 "API design", "thiết kế API", "REST API"  
📂 `\.claude\skills\api\api-design-patterns`

**api-documentation-writer**  
Generate comprehensive API documentation for REST, GraphQL, WebSocket APIs with OpenAPI specs, endpoint descriptions, request/response examples, er...  
🔍 "viết document API", "tạo API docs", "generate API documentation"  
📂 `\.claude\skills\api\api-documentation-writer`

### AUTH (1 skills)

**auth-implementation-patterns**  
Master authentication and authorization patterns including JWT, OAuth2, session management, and RBAC to build secure, scalable access control systems  
🔍 "authentication", "xác thực", "authorization"  
📂 `\.claude\skills\auth\auth-implementation-patterns`

### BACKEND (2 skills)

**backend-dev-guidelines**  
Node.js/Express/TypeScript microservices development  
🔍 "backend development", "phát triển backend", "Node.js"  
📂 `\.claude\skills\backend\backend-dev-guidelines`

**fastapi-templates**  
Create production-ready FastAPI projects with async patterns, dependency injection, and comprehensive error handling  
🔍 "FastAPI", "Python backend", "async API"  
📂 `\.claude\skills\backend\fastapi-templates`

### CODE_REVIEW (1 skills)

**code-review-excellence**  
Master effective code review practices to provide constructive feedback, catch bugs early, and foster knowledge sharing while maintaining team morale  
🔍 "code review", "review code", "pull request"  
📂 `\.claude\skills\code_review\code-review-excellence`

### COMMON (2 skills)

**git-commit-helper**  
Generate descriptive commit messages by analyzing git diffs  
🔍 "commit message", "git commit", "commit helper"  
📂 `\.claude\skills\common\git-commit-helper`

**repomix**  
Package entire code repositories into single AI-friendly files using Repomix  
🔍 "Repomix", "package repository", "pack codebase"  
📂 `\.claude\skills\common\repomix`

### CONTENT_MARKETING (2 skills)

**product-search-scoring**  
Advanced product search system with keyword scoring, Vietnamese text normalization, multi-field matching, and search result ranking  
🔍 "product search", "tìm kiếm sản phẩm", "search scoring"  
📂 `\.claude\skills\content_marketing\product-search-scoring`

**seo-optimization**  
Comprehensive SEO optimization using official Google guidelines  
🔍 "SEO optimization", "optimize SEO", "SEO content"  
📂 `\.claude\skills\content_marketing\seo-optimization`

### CRITICAL_THINKING (2 skills)

**brainstorming**  
Use when creating or developing, before writing code or implementation plans - refines rough ideas into fully-formed designs through collaborative ...  
🔍 "brainstorm", "brainstorming", "idea development"  
📂 `\.claude\skills\critical_thinking\brainstorming`

**sequential-thinking**  
Use when complex problems require systematic step-by-step reasoning with ability to revise thoughts, branch into alternative approaches, or dynamic...  
🔍 "sequential thinking", "step-by-step reasoning", "problem decomposition"  
📂 `\.claude\skills\critical_thinking\sequential-thinking`

### DATABASE (8 skills)

**database-schema-diff**  
Leverages database-diff-tool plugin to compare schemas, generate migration scripts, and provide rollback procedures for PostgreSQL/MySQL  
🔍 "database diff", "schema comparison", "so sánh schema"  
📂 `\.claude\skills\database\database-schema-diff`

**database-seeding**  
Generate realistic database seed data and test fixtures for development, testing, and demos  
🔍 "seed data", "tạo seed data", "database seeding"  
📂 `\.claude\skills\database\database-seeding`

**database-security-audit**  
Comprehensive database security scanning and data integrity validation  
🔍 "database security", "bảo mật database", "security scan"  
📂 `\.claude\skills\database\database-security-audit`

**database-essentials**  
Work with MongoDB (document database, BSON, aggregation pipelines, Atlas) and PostgreSQL (relational database, SQL, psql, pgAdmin)  
🔍 "MongoDB", "PostgreSQL", "postgres"  
📂 `\.claude\skills\database\database-essentials`

**database-schema-design**  
Design, visualize, and document database schemas with ERD generation, normalization guidance (1NF-BCNF), relationship mapping, and automated docume...  
🔍 "design schema", "thiết kế schema", "ERD diagram"  
📂 `\.claude\skills\database\database-schema-design`

**orm-code-generator**  
Generate ORM models and database schemas for TypeORM, Prisma, Sequelize, SQLAlchemy, Django ORM, Entity Framework, and Hibernate  
🔍 "ORM model", "tạo ORM", "generate entity"  
📂 `\.claude\skills\database\orm-code-generator`

**database-backup**  
Safe database migration workflow with Spatie backup integration  
🔍 "database backup", "backup database", "sao lưu database"  
📂 `\.claude\skills\database\database-backup`

**database-performance-optimization**  
Master database and SQL query optimization through index analysis, EXPLAIN plans, query profiling, N+1 pattern fixes, efficient pagination, and per...  
🔍 "optimize database", "tối ưu database", "slow query"  
📂 `\.claude\skills\database\database-performance-optimization`

### DEBUG (1 skills)

**systematic-debugging**  
Four-phase debugging framework ensuring root cause investigation before fixes  
🔍 "debugging", "debug", "gỡ lỗi"  
📂 `\.claude\skills\debug\systematic-debugging`

### DOCUMENTS (2 skills)

**docs-seeker**  
Searching internet for technical documentation using llms.txt standard, GitHub repositories via Repomix, and parallel exploration  
🔍 "find documentation", "tìm tài liệu", "search docs"  
📂 `\.claude\skills\documents\docs-seeker`

**writing-plans**  
Use when design is complete and you need detailed implementation tasks for engineers with zero codebase context - creates comprehensive implementat...  
🔍 "writing plans", "implementation plan", "kế hoạch triển khai"  
📂 `\.claude\skills\documents\writing-plans`

### FILAMENT (3 skills)

**filament-form-debugger**  
Diagnose and fix common Filament 4.x form errors - namespace issues (Tabs/Grid/Get), type mismatch, trait errors  
🔍 "Class not found", "Argument must be of type", "Trait not found"  
📂 `\.claude\skills\filament\filament-form-debugger`

**image-management**  
Centralized polymorphic image management with CheckboxList picker, WebP auto-conversion, order management (order=0 for cover), soft deletes  
🔍 "image management", "quản lý ảnh", "image gallery"  
📂 `\.claude\skills\filament\image-management`

**filament-resource-generator**  
Automated Filament 4.x resource generation following coding standards (Schema namespace, Vietnamese labels, Observer patterns, ImagesRelationManager)  
🔍 "tạo resource mới", "create new resource", "generate Filament resource"  
📂 `\.claude\skills\filament\filament-resource-generator`

### FRAMEWORK (4 skills)

**laravel**  
Laravel v12 - The PHP Framework For Web Artisans  
🔍 "Laravel", "Laravel 12", "Eloquent"  
📂 `\.claude\skills\framework\laravel`

**laravel-dusk**  
Laravel Dusk - Browser automation and testing tool for Laravel applications  
🔍 "Laravel Dusk", "Dusk testing", "browser automation Laravel"  
📂 `\.claude\skills\framework\laravel-dusk`

**laravel-prompts**  
Laravel Prompts - Beautiful and user-friendly forms for command-line applications with browser-like features including placeholder text and validat...  
🔍 "Laravel Prompts", "CLI prompts", "Artisan prompts"  
📂 `\.claude\skills\framework\laravel-prompts`

**nextjs**  
Use this skill for Next.js App Router patterns, Server Components, Server Actions, Cache Components, and framework-level optimizations  
🔍 "Next.js", "Next.js 16", "App Router"  
📂 `\.claude\skills\framework\nextjs`

### FRONTEND (6 skills)

**frontend-dev-guidelines**  
Frontend development guidelines covering component patterns, file organization, data fetching, loading/error states, performance, routing, styling,...  
🔍 "frontend guidelines", "frontend best practices", "frontend patterns"  
📂 `\.claude\skills\frontend\frontend-dev-guidelines`

**frontend-responsive**  
Build responsive, mobile-first layouts using fluid containers, flexible units, media queries, and touch-friendly design that works across all scree...  
🔍 "responsive design", "mobile-first", "breakpoints"  
📂 `\.claude\skills\frontend\frontend-responsive`

**zustand-state-management**  
Production-tested setup for Zustand state management in React applications with TypeScript  
🔍 "Zustand", "state management", "React state"  
📂 `\.claude\skills\frontend\zustand-state-management`

**ui-ux-design-system**  
Expert UI/UX design and design system toolkit  
🔍 "design", "UI", "styling"  
📂 `\.claude\skills\frontend\ui-ux-design-system`

**react-component-architecture**  
Design scalable, reusable React components following single responsibility principle with functional components, hooks, composition patterns, TypeS...  
🔍 "React architecture", "React components", "React hooks"  
📂 `\.claude\skills\frontend\react-component-architecture`

**ui-styling**  
Create beautiful, accessible user interfaces with shadcn/ui components (Radix UI + Tailwind), Tailwind CSS utility-first framework, responsive desi...  
🔍 "shadcn/ui", "shadcn", "Radix UI"  
📂 `\.claude\skills\frontend\ui-styling`

### META (1 skills)

**create-skill**  
Tạo Agent Skills mới cho AI AGENT theo đúng chuẩn và best practices  
🔍 "tạo skill", "viết skill", "create skill"  
📂 `\.claude\skills\meta\create-skill`

### OPTIMIZE (2 skills)

**cache-optimization**  
Analyze and improve application caching strategies  
🔍 "cache optimization", "optimize cache", "cache performance"  
📂 `\.claude\skills\optimize\cache-optimization`

**web-performance-audit**  
Conduct comprehensive web performance audits  
🔍 "performance audit", "web performance", "page speed"  
📂 `\.claude\skills\optimize\web-performance-audit`

### TESTING (2 skills)

**qa-verification**  
Comprehensive truth scoring, code quality verification, and automatic rollback system with 0.95 accuracy threshold for ensuring high-quality agent ...  
🔍 "QA verification", "quality assurance", "truth scoring"  
📂 `\.claude\skills\testing\qa-verification`

**playwright-e2e-testing**  
Complete E2E testing and browser automation with Playwright  
🔍 "E2E testing", "end-to-end testing", "Playwright"  
📂 `\.claude\skills\testing\playwright-e2e-testing`

---

## How to Load Full Details

When you match a skill from this index:

1. **Extract path** from matched skill (e.g., `\.claude\skills\database\database-performance-optimization`)
2. **Load full skill file**: 
   ```
   Read: E:\Laravel\study\skill_system\.claude\hook\choose_skill.md\skills\{domain}\{skill-name}.md
   ```
3. **Get complete info**: Full description, all triggers, detailed capabilities

**Why This Approach?**
- Initial scan: ~5 KB (this index) instead of ~27 KB (full active_skill.md)
- **82% context saved** on every skill lookup
- Progressive disclosure: Load details only when needed

---

*Generated automatically from active_skill.md*  
*To regenerate: `python .claude/skills/meta/create_skill/scripts/generate_skill_index.py`*
