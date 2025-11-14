# Bước 1: Phân Tích Task

## 1.1 Viết Lại Yêu Cầu
- **Yêu cầu gốc**: {User request}
- **Yêu cầu rõ ràng**: {Paraphrase clear và specific}

## 1.2 Phân Loại
- **Loại**: Tạo mới/Phân tích/Chuyển đổi/Validate/Review/Tối ưu
- **Domain**: API/UI-UX/Data/Git/Documents/Code
- **Độ khó**: Dễ ⭐ / Trung bình ⭐⭐ / Khó ⭐⭐⭐ / Rất khó ⭐⭐⭐⭐
- **Lý do độ khó**: {Giải thích}

## 1.3 Chia Nhỏ (nếu phức tạp)
- Subtask 1, 2, 3...
---

# Bước 2: Chọn Skill Combo

## CASE A: User KHÔNG Chỉ Định Skill
1. Đọc `\.claude\hook\choose_skill.md\active_skill.md`
2. Match skills (triggers, file types, operations)
3. Tạo 1-2 combos tốt nhất
4. Trình bày:
```
Combo 1: {name} (đề xuất)
- Skills: {skill-1} + {skill-2}
- Steps: 1) {step1}, 2) {step2}
- ✅ Mạnh: {pros} | ⚠️ Yếu: {cons}

Combo 2: {name}
...
```

## CASE B: User ĐÃ Chỉ Định
→ Skip combo, xác nhận dùng skill {skill-name}

---

# Bước 3: Load Skills
1. Đọc SKILL.md: `\.claude\skills\{group}\{skill-name}\SKILL.md`
2. Load theo Level: 1 (Overview) → 2 (Quick Start) → 3 (References on-demand)
3. Check Prerequisites (tools, dependencies, permissions)
4. Load reference files khi cần (workflow.md, examples.md, validation.md)

---

# Bước 3.5: Show Todos (TRƯỚC KHI EXECUTE)
**Tạo và trình bày todos list** cho user chốt:

```
📋 **Todos List**:

1. [{priority}] {Action} - {Lý do/mục đích} (Optional/Required)
2. [{priority}] {Action} - {Lý do/mục đích} (Optional/Required)
3. [{priority}] {Action} - {Lý do/mục đích} (Optional/Required)
...

Ví dụ:
1. [HIGH] Tạo folder structure - Chuẩn bị cấu trúc skill (Required)
2. [HIGH] Viết SKILL.md frontmatter - Name và description (Required)
3. [MEDIUM] Tạo reference/workflow.md - Chi tiết 11 steps (Optional)
4. [HIGH] Đăng ký vào active_skill.md - Để skill activate được (Required)
5. [MEDIUM] Test skill activation - Verify triggers work (Optional)

**Required**: Bước bắt buộc phải làm
**Optional**: Bước có thể bỏ qua hoặc làm sau

Bạn chốt todos list này không? (Có thể đề xuất thay đổi)
```

**Quy tắc todos**:
- Gọn gàng, mỗi item 1 dòng
- Có priority: HIGH/MEDIUM/LOW
- Có lý do/mục đích ngắn gọn
- Đánh dấu Optional/Required rõ ràng
- Sắp xếp theo thứ tự thực hiện

---

- Khi làm xong hoặc hỏi lại người hỏi hãy phát ra âm thật lớn [Console]::Beep(4000, 3000) 

# Bước 4: Execute Todos
Sau khi user chốt todos:

1. **Execute từng item** theo thứ tự
2. **Update progress**: ✅ Done | 🔄 Doing | ⏳ Pending
3. **Handle errors**: Check Troubleshooting → Try alternatives → Fallback
4. **Validate**: Format, quality, expectations

---

# Bước 5: Complete
1. **Present results**:
   - ✅ Completed: {what}
   - Outputs: {list}
   - Suggestions: {next steps}

2. **Feedback**: Satisfied? → Done | Adjust? → Iterate
---

## Tips
- Luôn phân tích task trước
- 1 skill > 2 skills nếu đủ
- Progressive loading (Level 1→2→3)
- Show todos trước execute
- Handle errors gracefully
