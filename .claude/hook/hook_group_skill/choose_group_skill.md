# Hướng Dẫn Chọn Group Skill
File này giúp AI AGENTS xác định Group Skill phù hợp với yêu cầu của người dùng.
Các group skill nằm ở \.claude\skills\{group_skill_name}

## Các Group Skill Có Sẵn

### 1. **api/** - API & Integration
- Dùng cho: REST API, GraphQL, tích hợp API bên ngoài
- Ví dụ: "API endpoint", "Integrate Stripe"

### 2. **architecture/** - Kiến Trúc & Thiết Kế Hệ Thống
- Dùng cho: Kiến trúc hệ thống, design patterns, cấu trúc thư mục
- Ví dụ: "Microservices", "Cấu trúc dự án", "Design pattern"

### 3. **auth/** - Xác Thực & Phân Quyền
- Dùng cho: JWT, OAuth, RBAC, phân quyền, đăng nhập
- Ví dụ: "JWT authentication", "Phân quyền", "Middleware xác thực"

### 4. **backend/** - Phát Triển Backend
- Dùng cho: Logic backend, controllers, services, business logic
- Ví dụ: "API handler", "Service xử lý", "Backend validation"

### 5. **code_review/** - Đánh Giá & Cải Thiện Code
- Dùng cho: Review code, cải tiến, refactoring, best practices
- Ví dụ: "Review code", "Cải thiện performance", "Code quality"

### 6. **common/** - Skill Đặc Thù Dự Án
- Dùng cho: Skill cụ thể & đặc thù cho dự án
- Ví dụ: "Chấm điểm từ khóa", "Search logic", "Custom utilities"

### 7. **content_marketing/** - Nội Dung & Marketing
- Dùng cho: Blog posts, marketing copy, documentation
- Ví dụ: "Description sản phẩm", "Blog post", "README"

### 8. **critical_thinking/** - Phân Tích & Giải Quyết Vấn Đề
- Dùng cho: Phân tích vấn đề, root cause, strategic thinking
- Ví dụ: "Hiệu năng chậm?", "Root cause", "Giải pháp"

### 9. **database/** - Cơ Dữ Liệu & SQL
- Dùng cho: Schema, SQL queries, migrations, optimization
- Ví dụ: "Bảng users", "SQL query", "Migration", "Optimize"

### 10. **debug/** - Gỡ Lỗi & Troubleshooting
- Dùng cho: Sửa lỗi, debugging, troubleshooting
- Ví dụ: "Code lỗi", "Component không render", "Debug error"

### 11. **documents/** - Tài Liệu & Documentation
- Dùng cho: Tài liệu, comments code, API docs
- Ví dụ: "Documentation", "Comments code", "Setup guide"

### 12. **framework/** - Framework & Library
- Dùng cho: Laravel, React, Vue, Angular, Tailwind, Next.js
- Ví dụ: "Laravel", "React hooks", "Vue composition"

### 13. **frontend/** - Phát Triển Frontend
- Dùng cho: Components, layouts, styling, logic client-side
- Ví dụ: "Component", "CSS", "Form", "Responsive"

### 14. **meta/** - Quản Lý & Cải Tiến Hệ Thống Skill
- Dùng cho: Tạo skill mới, refactor skill, thêm khả năng, optimize
- Ví dụ: "Skill chấm điểm", "Refactor search", "Phân tích skill", "Cải tiến"

### 15. **optimize/** - Tối Ưu Hóa & Performance
- Dùng cho: Performance, caching, code optimization, refactor
- Ví dụ: "Optimize query", "Bundle size", "Cache strategy"

### 16. **research/** - Nghiên Cứu & Tìm Hiểu
- Dùng cho: Tìm hiểu công nghệ, research, investigation
- Ví dụ: "WebSocket", "So sánh database", "Best practice"

### 17. **security/** - Bảo Mật & An Toàn
- Dùng cho: Bảo mật, SQL injection, CSRF, validation, encryption
- Ví dụ: "SQL injection", "Validate input", "Encryption", "Security"

### 18. **testing/** - Test & QA
- Dùng cho: Unit tests, integration tests, E2E tests, coverage
- Ví dụ: "Unit test", "E2E testing", "Jest", "Test coverage"

## Quy Trình Xác Định Group Skill
1. Đọc yêu cầu - Hiểu rõ nhu cầu
2. Xác định lĩnh vực - Frontend/Backend/API/Database?
3. Xác định tính chất - Build/Debug/Refactor/Optimize/Test?
4. Chọn group skill phù hợp
5. Kết hợp thêm group liên quan (Security, Testing, Optimization)

## Ví Dụ
| Yêu Cầu | Group Chính | Liên Quan |
|---------|-----------|----------|
| API login JWT | `auth/` + `api/` | `security/`, `backend/`, `framework/` |
| Code chạy chậm | `optimize/` | `debug/`, `database/`, `critical_thinking/` |
| Component lỗi | `debug/` | `frontend/`, `framework/` |
| Thiết kế DB | `database/` + `architecture/` | `backend/`, `security/` |
| Review code | `code_review/` | `optimize/`, `security/`, `testing/` |

## Ghi Chú
- Dùng **1 hoặc nhiều** group skill
- Ưu tiên group **cụ thể nhất**
- Khi không chắc: kết hợp **2-3 group**
- Group chung (`common/`, `documents/`) dùng kèm bất kỳ group nào