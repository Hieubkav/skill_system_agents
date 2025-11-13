# Hướng Dẫn Chọn Group Skill

## TOC
- [Group Skill](#group-skill)
- [Ví Dụ](#ví-dụ)

---

## Group Skill

| Group | Mục Đích | Ví Dụ |
|-------|---------|-------|
| **api/** | REST/GraphQL, tích hợp API | "Tạo API", "Integrate Stripe" |
| **architecture/** | Thiết kế hệ thống, design pattern | "Thiết kế microservices", "Cấu trúc dự án" |
| **auth/** | JWT, OAuth, login, RBAC | "Implement JWT", "Phân quyền" |
| **backend/** | Controllers, services, business logic | "API handler", "Business logic" |
| **code_review/** | Review code, refactor, best practices | "Review code", "Refactor" |
| **common/** | Skill đặc thù dự án cho 1 việc cụ thể | "Chấm điểm từ khóa", "Search logic" |
| **content_marketing/** | Blog posts, viết cho dễ SEO | "Viết blog", "SEO optimization" |
| **critical_thinking/** | tăng cường suy nghĩ của AI AGENT để giải quyết vấn đề khó | "Brandstorm vấn đề" |
| **database/** | Schema, SQL, migrations, optimize | "Tạo bảng", "SQL query", "Optimize" |
| **debug/** | Sửa lỗi, troubleshooting | "Code bị lỗi", "Debug error" |
| **documents/** | Tài liệu, comments, API docs | "Viết docs", "Comment code" |
| **framework/** | Laravel, React, Vue, Tailwind, etc | "Laravel patterns", "React hooks" |
| **frontend/** | UI/UX, components, styling, responsive | "Component", "CSS", "Form validation" |
| **meta/** | Tạo/refactor skill, cải tiến hệ thống | "Tạo skill mới", "Enhance skill" |
| **optimize/** | Performance, caching, efficiency | "Tối ưu query", "Reduce bundle" |
| **research/** | Tìm hiểu công nghệ, investigation | "Tìm hiểu WebSocket", "So sánh DB", "Tìm github của Convex" |
| **security/** | Validation, encryption, injection | "Prevent SQL injection", "Encrypt" |
| **testing/** | Unit test, E2E, coverage, strategy | "Unit test", "E2E test", "Jest" |

## Ví Dụ

| Yêu Cầu | Group Chính | Phụ Trợ |
|--------|-----------|--------|
| "Tạo API login + JWT" | `auth/`, `api/` | `security/`, `backend/` |
| "Code chạy chậm" | `optimize/` | `debug/`, `database/` |
| "Sửa lỗi component" | `debug/` | `frontend/`, `framework/` |
| "Thiết kế database" | `database/`, `architecture/` | `backend/`, `security/` |
| "Review code" | `code_review/` | `optimize/`, `security/` |
