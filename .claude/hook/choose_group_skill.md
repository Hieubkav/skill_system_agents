# Hướng Dẫn Chọn Group Skill
File này giúp AI AGENTS xác định Group Skill phù hợp với yêu cầu của người dùng.

## Table of Contents

- [Các Group Skill Có Sẵn](#các-group-skill-có-sẵn)
- [Quy Trình Xác Định Group Skill](#quy-trình-xác-định-group-skill)
- [Ví Dụ](#ví-dụ)
- [Ghi Chú](#ghi-chú)

---

## Các Group Skill Có Sẵn

### 1. **api/** - API & Integration
- Dùng cho: Xây dựng API endpoints, REST API, GraphQL, tích hợp API bên ngoài
- Ví dụ: "Tạo API endpoint", "Integrate Stripe", "Tạo GraphQL schema"

### 2. **architecture/** - Kiến Trúc & Thiết Kế Hệ Thống
- Dùng cho: Thiết kế kiến trúc hệ thống, design patterns, cấu trúc thư mục, blueprint hệ thống
- Ví dụ: "Thiết kế kiến trúc microservices", "Tạo cấu trúc dự án", "Design pattern nào phù hợp?"

### 3. **auth/** - Xác Thực & Phân Quyền
- Dùng cho: Đăng nhập, đăng ký, JWT, OAuth, Role-based access control (RBAC), phân quyền
- Ví dụ: "Implement JWT authentication", "Tạo hệ thống phân quyền", "Middleware xác thực"

### 4. **backend/** - Phát Triển Backend
- Dùng cho: Xây dựng logic backend, controllers, services, business logic, server-side
- Ví dụ: "Viết API handler", "Tạo service xử lý business logic", "Backend validation"

### 5. **code_review/** - Đánh Giá & Cải Thiện Code
- Dùng cho: Review code, suggest improvements, refactoring, best practices
- Ví dụ: "Review đoạn code này", "Cải thiện performance", "Code quality check"

### 6. **common/** - Skill Đặc Thù Dự Án
- Dùng cho: Các skill cực kỳ cụ thể & đặc thù cho dự án không phải chung chung
- Ví dụ: "Chấm điểm từ khóa", "Search logic dự án", "Business logic đặc biệt", "Custom utilities dự án"

### 7. **content_marketing/** - Nội Dung & Marketing
- Dùng cho: Viết content, blog posts, marketing copy, documentation
- Ví dụ: "Viết description sản phẩm", "Tạo blog post", "Write README"

### 8. **critical_thinking/** - Phân Tích & Giải Quyết Vấn Đề
- Dùng cho: Phân tích vấn đề, xác định nguyên nhân gốc, strategic thinking
- Ví dụ: "Tại sao hiệu năng chậm?", "Xác định root cause", "Giải pháp cho bài toán"

### 9. **database/** - Cơ Dữ Liệu & SQL
- Dùng cho: Thiết kế schema, SQL queries, migrations, database optimization
- Ví dụ: "Tạo bảng users", "Viết SQL query", "Database migration", "Optimize query"

### 10. **debug/** - Gỡ Lỗi & Troubleshooting
- Dùng cho: Xác định và sửa lỗi, debugging, troubleshooting
- Ví dụ: "Code bị lỗi", "Tại sao component không render?", "Debug error này"

### 11. **documents/** - Tài Liệu & Documentation
- Dùng cho: Viết tài liệu, comment code, API documentation, setup guides
- Ví dụ: "Viết documentation", "Thêm comments vào code", "Setup guide"

### 12. **framework/** - Framework & Library
- Dùng cho: Laravel, React, Vue, Angular, Django, Spring, Tailwind, Next.js, v.v.
- Ví dụ: "Laravel best practices", "React hooks", "Vue composition API"

### 13. **frontend/** - Phát Triển Frontend
- Dùng cho: UI/UX, components, layouts, styling, client-side logic
- Ví dụ: "Tạo component", "CSS styling", "Form validation", "Responsive design"

### 14. **meta/** - Quản Lý & Cải Tiến Hệ Thống Skill
- Dùng cho: Tạo skill mới, refactor skill hiện có, thêm khả năng cho skill, optimize hệ thống skill
- Ví dụ: "Tạo skill mới cho chấm điểm", "Refactor lại skill search", "Thêm khả năng phân tích cho skill hiện có", "Cải tiến hệ thống skill"

### 15. **optimize/** - Tối Ưu Hóa & Performance
- Dùng cho: Improve performance, caching, optimize code, refactor, efficiency
- Ví dụ: "Tối ưu query database", "Reduce bundle size", "Cache strategy"

### 16. **research/** - Nghiên Cứu & Tìm Hiểu
- Dùng cho: Tìm hiểu công nghệ mới, research, investigation, exploration
- Ví dụ: "Tìm hiểu về WebSocket", "So sánh các database", "Research best practice"

### 17. **security/** - Bảo Mật & An Toàn
- Dùng cho: Xử lý bảo mật, SQL injection, CSRF, validation, encryption, XSS prevention
- Ví dụ: "Prevent SQL injection", "Validate user input", "Encryption", "Security audit"

### 18. **testing/** - Test & QA
- Dùng cho: Unit tests, integration tests, E2E tests, test coverage, testing strategy
- Ví dụ: "Viết unit test", "Test this function", "E2E testing", "Jest configuration"

## Quy Trình Xác Định Group Skill

1. **Đọc yêu cầu từ người dùng** - Hiểu rõ họ cần gì
2. **Xác định lĩnh vực chính** - Liệu là Frontend, Backend, API, Database, v.v.?
3. **Xác định tính chất công việc** - Là build, debug, refactor, research, optimize, hay test?
4. **Chọn Group Skill phù hợp nhất** - Có thể sử dụng nhiều group nếu cần
5. **Kết hợp với Group Khác** - Security, Testing, Optimization nên được xem xét thêm

## Ví Dụ

### Yêu Cầu: "Tạo API endpoint login với JWT authentication"
- **Group Chính**: `auth/` + `api/`
- **Liên Quan**: `security/`, `backend/`, `framework/`

### Yêu Cầu: "Code chạy chậm, cần tối ưu"
- **Group Chính**: `optimize/`
- **Liên Quan**: `debug/`, `database/`, `critical_thinking/`

### Yêu Cầu: "Sửa lỗi component không render"
- **Group Chính**: `debug/`
- **Liên Quan**: `frontend/`, `framework/`

### Yêu Cầu: "Thiết kế cơ sở dữ liệu cho hệ thống bán hàng"
- **Group Chính**: `database/` + `architecture/`
- **Liên Quan**: `backend/`, `security/`

### Yêu Cầu: "Review code và suggest improvements"
- **Group Chính**: `code_review/`
- **Liên Quan**: `optimize/`, `security/`, `testing/`

## Ghi Chú

- Mỗi yêu cầu có thể sử dụng **1 hoặc nhiều group skill**
- Ưu tiên chọn group skill **cụ thể nhất** trước
- Nếu không chắc chắn, kết hợp **2-3 group skill** để đảm bảo đủ context
- **Group Chung** (`common/`, `documents/`) có thể được dùng kèm với bất kỳ group nào khác
