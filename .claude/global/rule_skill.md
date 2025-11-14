## Luật của hệ thống skill 

### Triết lý 4 tầng tiết lộ tiến bộ
Tránh AI AGENT đọc hết, tiết kiệm context, tăng độ chính xác.
- **Tầng 0** (luôn tải): README.md nhóm - tổng quan, danh sách skill,để chọn skill
- **Tầng 1** (luôn tải): README.md skill - metadata, thông tin cơ bản cần thiết
- **Tầng 2** (khi chọn skill): SKILL.md - tổng quan, quickstart, bảng tham chiếu reference.md (chỉ tên + mô tả ngắn + khi nào gọi)
- **Tầng 3** (khi cần): reference.md - chi tiết, ví dụ, code mẫu, trường hợp sử dụng cụ thể

### Quy tắc của file SKILL.md

#### Cấu trúc bắt buộc

File SKILL.md phải tuân thủ cấu trúc sau đây:

```yaml
---
name: "Your Skill Name"
description: "What it does and when to use it"
---

# Your Skill Name

## Level 1: Overview (Always Read First)
Brief 2-3 sentence description of the skill.

## Prerequisites
- Requirement 1
- Requirement 2

## What This Skill Does
1. Primary function
2. Secondary function
3. Key benefit

---

## Level 2: Quick Start (For Fast Onboarding)

### Basic Usage
```bash
# Simplest use case
command --option value
```

### Key Steps
1. Step 1
2. Step 2
3. Step 3

---

## Level 3: Reference Guides
- [reference-1.md](./reference-1.md) - Use when: describe when to use this
- [reference-2.md](./reference-2.md) - Use when: describe when to use this
- [reference-3.md](./reference-3.md) - Use when: describe when to use this

---

## Common Use Cases
- Case 1: ...
- Case 2: ...
- Case 3: ...

## Best Practices
- Practice 1
- Practice 2
- Practice 3

## Troubleshooting
| Issue | Solution |
|-------|----------|
| Problem 1 | Solution 1 |
| Problem 2 | Solution 2 |
```

#### Giới hạn kích thước
- **Tối đa 200 dòng**: Nếu nội dung vượt quá, đẩy phần chi tiết sang các `reference.md` hợp lý

---

#### Thành phần metadata (Frontmatter)

##### `name` (YÊU CẦU)
| Tiêu chí | Chi tiết |
|---------|---------|
| **Kiểu** | Chuỗi |
| **Độ dài** | Tối đa 64 ký tự |
| **Định dạng** | Tên thân thiện với người dùng, chữ hoa đầu dòng, mô tả ngắn gọn |

**Ví dụ đúng (✅)**:
- "Trình tạo tài liệu API"
- "Trình tạo thành phần React"
- "Trình thiết kế lược đồ cơ sở dữ liệu"

**Ví dụ sai (❌)**:
- "kỹ năng-1" — không mô tả, quá chung chung
- "Đây là tên kỹ năng rất dài, vượt quá giới hạn sáu mươi bốn ký tự" — vượt quá giới hạn

---

##### `description` (YÊU CẦU)
| Tiêu chí | Chi tiết |
|---------|---------|
| **Kiểu** | Chuỗi |
| **Độ dài** | Tối đa 1024 ký tự |
| **Định dạng** | Văn bản thuần túy hoặc Markdown tối thiểu |

**Nội dung bắt buộc phải bao gồm**:
1. **Chức năng**: Kỹ năng này làm gì (mô tả rõ ràng)
2. **Điều kiện kích hoạt**: Khi nào Claude nên gọi kỹ năng này (tình huống sử dụng)

**Gợi ý tốt nhất**:
- Liệt kê cụ thể các từ khóa kích hoạt
- Nêu các trường hợp sử dụng cụ thể và rõ ràng
- Tránh dùng từ ngữ mơ hồ hoặc quá chung chung

**Ví dụ đúng (✅)**:
- "Tạo tài liệu OpenAPI 3.0 từ các tuyến Express.js. Sử dụng khi tạo tài liệu API, ghi lại endpoint hoặc xây dựng thông số kỹ thuật API."
- "Tạo các thành phần chức năng React bằng TypeScript, hook và unit test. Sử dụng khi xây dựng các thành phần UI mới hoặc chuyển đổi class component sang functional component."

**Ví dụ sai (❌)**:
- "Hướng dẫn toàn diện về tài liệu API" — thiếu phần "khi nào sử dụng"
- "Công cụ tài liệu" — quá mơ hồ, không nêu rõ chức năng
- "Thứ gì đó liên quan đến API" — không đủ thông tin chi tiết

---

#### Các phần nội dung chính

| Phần | Mục đích | Bắt buộc |
|-----|---------|----------|
| **Level 1: Overview** | Mô tả nhanh 2-3 câu, đủ để quyết định có dùng hay không | ✅ |
| **Prerequisites** | Các điều kiện/yêu cầu cần thiết trước khi sử dụng | ✅ |
| **What This Skill Does** | Liệt kê chức năng chính và lợi ích | ✅ |
| **Level 2: Quick Start** | Hướng dẫn nhanh để bắt đầu sử dụng (ví dụ và bước) | ✅ |
| **Level 3: Reference Guides** | Danh sách các file reference.md với mô tả khi nào gọi | ✅ |
| **Common Use Cases** | Các kịch bản sử dụng thực tế | ✅ |
| **Best Practices** | Các lưu ý và cách làm tốt nhất | ✅ |
| **Troubleshooting** | Giải quyết vấn đề thường gặp | ✅ |

### Cấu trúc Folder
~/.claude/skills/
|
|---my_skill/
|   |---SKILL.md    # REQUIRED: Main skill file
|   |---README.md   # REQUIRED: Human-readable docs. Mô tả chủ đề của skill <100 ký tự
|   |---reference/  # Optional: Documentation intended to be loaded into context as needed
|   |   |---examples.md
|   |   |---features1.md 
|   |   |---features2.md
|   |   |---features3.md # 1 file 1 tính năng, max 300 dòng, >100 dòng → thêm TOC
|   |   
|   |---assets/      # Optional:used in output (templatesicons, fonts, etc.)
|   |   |---image.webp/file font chữ
|   |   |---icon.png
|   |   |---icon.svg
|   |
|   |---scripts/     # Optional: Executable scripts
|   |   |---script1.js
|   |   |---script2.py
|   |   |---script3.sh


