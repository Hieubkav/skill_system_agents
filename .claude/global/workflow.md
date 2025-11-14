# Skill System Workflow 

## Bước 1: Phân Tích Task
- Xác định **domain** (API/Database/Frontend/Backend/Testing/etc)
- Chia subtasks nếu phức tạp

## Bước 2: Match Skills
- Đọc `\.claude\hook\choose_skill.md\active_skill.md`
- Match dựa trên triggers và domain
- Nếu user chỉ định skill → dùng luôn
- Nếu không → chọn combo skill phù hợp nhất rồi show cho người dùng -> sau đó chạy [Console]::Beep(4000, 500)  để thông báo

## Bước 3: Load Skill Details
- Khi người dùng đã chốt combo
- Đọc `\.claude\skills\{group}\{skill-name}\SKILL.md`
- Progressive loading: Level 1 (Overview) → Level 2 (Quick Start) → Level 3 (References on-demand)
- Load reference files khi cần (workflow.md, examples.md)

## Bước 4: Execute
- Tạo todos (TodoWrite tool) nếu task phức tạp (>=3 steps)
- Execute từng bước
- Update todos progress
- Handle errors gracefully

## Bước 5: Complete
- Present kết quả
- Suggest next steps nếu có

---

## Notes
- Beep khi hoàn thành: `[Console]::Beep(4000, 500)`
- 1 skill > 2 skills nếu đủ
- Progressive loading tiết kiệm tokens
