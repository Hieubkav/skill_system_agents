## Luật của hệ thống skill 

### Triết lý hệ thống tiết lộ tiến bộ
- Hệ thống skill có 4 tầng tiết lộ tiến bộ, tránh AI AGENT đọc hết làm vừa tốn context, mất thời gian và giảm tính chính xác
- Tầng 0 (luôn tải): chọn ra nhóm của skill đó mỗi nhóm có README.md nói tổng quan về nhóm skill có gì để AI AGENT chọn ra skill nào mà lựa cho phù hợp.
- Tầng 1 (luôn tải): là file README.md của mỗi skill. Đây chính là metadata của skill đó. Cung cấp cho AI AGENT thông tin tối cần thiết về skill đó. 
- Tầng 2 (chỉ tải khi chọn skill): là file SKILL.md của mỗi skill. Đây là file chính của skill đó. Đọc để hiểu rõ skill như tổng quan, quickstart cũng như chỉ ra bảng đồ file reference.md rõ ràng. Nên nhớ chỉ tiết lộ tên và mô tả ngắn gọn về các reference để bộ trợ cho skill và khi nào gọi file đó. Tránh gọi ngay nặng context
- Tầng 3 (chỉ tải khi cần): là các reference.md cần thiết cho skill cũng là chi tiết trung tâm của hệ thống. Ở đây sẽ cung cấp các ví dụ , code mẫu, chi tiết sử dụng cho từng trường hợp cụ thể.

### Quy tắc của skill
- SKILL.md không được vượt quá 200 dòng. Nếu có quá nhiều thông tin hãy đẩy sang references hợp lý.
- 1 file reference.md phải đảm bảo nói vào trọng tâm 1 tính năng, khía cạnh của skills đó. 1 file chỉ tối đa 300 dòng. Nếu > 100 dòng → thêm Table of Contents ở đầu. Để AGENTS có thể đọc 1 phần của file nhanh chống biết file đó viết gì để truy cập. 
- Trong mỗi skill sẽ có file README.md sẽ là file cho cho người đọc và mô tả ngắn gọn về  chủ đề của skill đó. File này không vượt quá 100 ký tự.
- SKILL.md phải: Write clear descriptions (skill invocation depends on it). Start simple, expand later .Version your skills (v1.0.0, etc.). Test with multiple prompts

### SKILL.md Metadata (YAML Frontmatter)
---
name: "Skill Name" (≤64 ký tự)
description: "What it does + when to use" (≤200 ký tự)
version: "1.0.0"
allowed-tools: "Bash, Read, Write"
---
Description là critical - AI AGENT dùng nó để chọn skill từ 100+ khả năng
Ví dụ:
✅ "Extract text/tables from PDFs, fill forms. Use when working with PDFs"
❌ "Helps with documents"

### Avoid Deeply Nested References
❌ SKILL.md → reference.md → details.md (AGENT tải partial)
✅ SKILL.md → advanced.md, reference.md, examples.md (1 level deep)

### Path Handling 
❌ Never hardcode absolute paths
Read /home/user/project/config.json
✅ Use {baseDir} variable (portable)
Read {baseDir}/scripts/config.json

### Cấu trúc Folder
my_skill/
|---SKILL.md
|---README.md
|---reference/
|   |---reference1.md
|   |---reference2.md
|   |---reference3.md
|
|---assets/
|   |---image.webp/file font chữ
|   |---examples.md
|   |---features1.md
|   |---features2.md
|   |---features3.md


