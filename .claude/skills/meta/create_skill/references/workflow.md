# Skill Creation Workflow

Quy trình 11 bước chi tiết để tạo một Agent Skill hoàn chỉnh.

## Table of Contents

- [Step 1: Xác định phạm vi Skill](#step-1-xác-định-phạm-vi-skill)
- [Step 2: Chọn Skill location](#step-2-chọn-skill-location)
- [Step 3: Tạo cấu trúc Skill](#step-3-tạo-cấu-trúc-skill)
- [Step 4: Viết SKILL.md frontmatter](#step-4-viết-skillmd-frontmatter)
- [Step 5: Viết description hiệu quả](#step-5-viết-description-hiệu-quả)
- [Step 6: Cấu trúc nội dung Skill](#step-6-cấu-trúc-nội-dung-skill)
- [Step 7: Tạo supporting files](#step-7-tạo-supporting-files)
- [Step 8: Validate Skill](#step-8-validate-skill)
- [Step 9: Test Skill](#step-9-test-skill)
- [Step 10: Debug nếu cần](#step-10-debug-nếu-cần)
- [Step 11: Đăng ký Skill vào active_skill.md](#step-11-đăng-ký-skill-vào-active_skillmd)

---

## Step 1: Xác định phạm vi Skill

### Hỏi các câu sau:
- Skill này cung cấp khả năng cụ thể nào?
- Khi nào AI AGENT nên dùng skill này?
- Cần tools hoặc resources gì?
- Dùng cá nhân hay chia sẻ team?

### Nguyên tắc: One Skill = One Capability
- ✅ **Good**: "PDF form filling", "Excel data analysis"
- ❌ **Too broad**: "Document processing", "Data tools"

### Why This Matters
- Focused skills activate more reliably
- Easier to maintain and update
- Better user experience
- Clear ownership and responsibility

---

## Step 2: Chọn Skill location

### Personal Skills Location
**Path**: `~/.claude/skills/{group-skill-name}/{skill-name}`

### Chọn Group Skill
- Tham khảo: `.claude/hook/hook_group_skill/choose_group_skill.md`
- Group skill giúp tổ chức và quản lý skills dễ dàng hơn

### Common Groups
- `meta/` - Skills về skill system
- `frontend/` - UI/UX, React, Vue, Angular
- `backend/` - API, database, server
- `data/` - Data processing, analysis
- `git/` - Version control, commits
- `documents/` - PDF, Word, Excel
- `code-quality/` - Linting, testing, review
- `api/` - API design, documentation

---

## Step 3: Tạo cấu trúc Skill

### Cấu trúc folder chuẩn:
```
~/.claude/skills/{group-skill-name}/
|
|---my_skill/
|   |---SKILL.md    # REQUIRED: Main skill file <200 dòng
|   |---README.md   # REQUIRED: Mô tả chủ đề <100 ký tự
|   |---reference/  # Optional: Documentation
|   |   |---examples.md
|   |   |---feature1.md 
|   |   |---feature2.md
|   |   |---feature3.md # 1 file 1 tính năng, max 300 dòng
|   |   
|   |---assets/      # Optional: Templates, icons, fonts
|   |   |---image.webp
|   |   |---icon.png
|   |
|   |---scripts/     # Optional: Executable scripts
|   |   |---script1.js
|   |   |---script2.py
```

### Quy tắc quan trọng:
- ✅ **SKILL.md** và **README.md** là BẮT BUỘC
- ✅ **SKILL.md** < 200 dòng (content chính, ngắn gọn)
- ✅ **README.md** < 100 ký tự (mô tả human-readable)
- ✅ **reference/** chứa docs chi tiết, mỗi file 1 tính năng
- ✅ **reference/*.md** max 300 dòng, >100 dòng thêm TOC
- ✅ **assets/** cho templates, icons, fonts (dùng trong output)
- ✅ **scripts/** cho executable scripts

### Lệnh tạo structure:
```bash
mkdir -p ~/.claude/skills/{group-skill-name}/{skill-name}/reference
mkdir -p ~/.claude/skills/{group-skill-name}/{skill-name}/assets
mkdir -p ~/.claude/skills/{group-skill-name}/{skill-name}/scripts
```

---

## Step 4: Viết SKILL.md frontmatter

### Frontmatter bắt buộc:
```yaml
---
name: skill-name
description: Brief description of what this does and when to use it
---
```

### Yêu cầu field `name`:
- Chỉ dùng: **lowercase letters (a-z)**, **numbers (0-9)**, **hyphens (-)**
- Max **64 characters**
- Phải **khớp với tên folder**
- ✅ **Good**: `pdf-processor`, `git-commit-helper`, `api-v2-designer`
- ❌ **Bad**: `PDF_Processor` (uppercase, underscore), `Git Commits!` (spaces, special chars), `skill_name` (underscore)

### Yêu cầu field `description`:
- Max **1024 characters**
- Bao gồm CẢ **"what it does"** VÀ **"when to use it"**
- Dùng **trigger words** mà user sẽ nói
- Đề cập **file types** (.pdf, .xlsx, .json), **operations** (analyze, extract, generate), **context**

### Frontmatter optional:
```yaml
allowed-tools: Read, Grep, Glob
```

**Dùng khi**:
- Read-only Skills (không modify files)
- Security-sensitive workflows
- Limited-scope operations

---

## Step 5: Viết description hiệu quả

### Formula:
```
[What it does] + [When to use it] + [Key triggers]
```

### Ví dụ tốt:

**Example 1**:
```yaml
description: Extract text and tables from PDF files, fill forms, merge documents. Use when working with PDF files or when the user mentions PDFs, forms, or document extraction.
```

**Example 2**:
```yaml
description: Analyze Excel spreadsheets, create pivot tables, and generate charts. Use when working with Excel files, spreadsheets, or analyzing tabular data in .xlsx format.
```

**Example 3**:
```yaml
description: Tạo Agent Skills mới cho AI AGENT theo đúng chuẩn và best practices. Dùng khi người dùng muốn tạo, viết, thiết kế skill mới, hoặc cần trợ giúp với file SKILL.md, frontmatter, hoặc cấu trúc skill. Trigger words: tạo skill, viết skill, create skill, new skill, SKILL.md, skill structure.
```

### Tips viết description:
1. **Include specific file extensions**: .pdf, .xlsx, .json, .csv
2. **Mention common user phrases**: "analyze", "extract", "generate", "tạo", "xử lý"
3. **List concrete operations**: not generic verbs like "process" or "handle"
4. **Add context clues**: "Use when...", "For...", "Dùng khi..."
5. **Both languages**: Vietnamese và English for Vietnamese-speaking users

### Bad Examples (tránh):
```yaml
description: Helps with documents  # Quá vague
description: For data analysis     # Thiếu details
description: Process files         # Không có context
```

---

## Step 6: Cấu trúc nội dung Skill

### Template chuẩn cho SKILL.md:

```markdown
---
name: "skill-name"
description: "What it does and when to use it"
---

# Skill Name

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
\`\`\`bash
# Simplest use case
command --option value
\`\`\`

### Key Steps
1. Step 1
2. Step 2
3. Step 3

---

## Level 3: Reference Guides
- [reference-1.md](./reference/reference-1.md) - Use when: mô tả khi nào dùng
- [reference-2.md](./reference/reference-2.md) - Use when: mô tả khi nào dùng

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

### Structure Principles:

**Level 1: Overview**
- Always read first
- 2-3 sentences maximum
- High-level purpose

**Level 2: Quick Start**
- Basic usage examples
- Key steps (3-5 steps)
- Get started quickly

**Level 3: Reference Guides**
- Links to detailed docs
- "Use when:" clauses for each link
- Progressive disclosure

**Keep SKILL.md < 200 dòng**:
- Move detailed instructions to reference/
- Move extensive examples to examples.md
- Move validation to validation.md
- Keep only essentials in SKILL.md

---

## Step 7: Tạo supporting files

### README.md (REQUIRED)
- Mô tả ngắn gọn chủ đề của skill
- **Max 100 ký tự**
- Viết cho con người đọc
- One-liner về purpose

**Example**:
```markdown
# Create Skill

Meta skill để tạo Agent Skills mới cho AI AGENT theo đúng chuẩn và best practices.
```

### reference/*.md (OPTIONAL)
- Detailed API docs, advanced options
- Extended examples and use cases
- Mỗi file tập trung 1 tính năng
- **Max 300 dòng per file**
- **>100 dòng thêm TOC**

**Common reference files**:
- `workflow.md` - Step-by-step detailed workflow
- `validation.md` - Validation checklists
- `examples.md` - Comprehensive examples
- `registration.md` - Registration guides
- `api.md` - API documentation
- `best-practices.md` - Advanced best practices

### scripts/ (OPTIONAL)
- Helper scripts và utilities
- Đặt tên rõ ràng: `process_data.py`, `validate.sh`, `transform.js`
- Include shebang for executability
- Document script parameters

### assets/ (OPTIONAL)
- Templates, icons, fonts
- File templates hoặc boilerplate
- Images for documentation
- Used in output generation

---

## Step 8: Validate Skill

### File structure checklist:
- [ ] ✅ SKILL.md exists in correct location
- [ ] ✅ SKILL.md < 200 dòng
- [ ] ✅ README.md exists
- [ ] ✅ README.md < 100 ký tự
- [ ] ✅ Directory name matches frontmatter `name`
- [ ] ✅ reference/ files (if any) < 300 dòng each

### YAML frontmatter checklist:
- [ ] ✅ Opening `---` on line 1
- [ ] ✅ Closing `---` before content
- [ ] ✅ Valid YAML (no tabs, correct indentation)
- [ ] ✅ `name` follows naming rules (lowercase, hyphens, ≤64 chars)
- [ ] ✅ `description` is specific and < 1024 chars
- [ ] ✅ `description` includes "what" and "when"

### Content quality checklist:
- [ ] ✅ Clear instructions for AI AGENT
- [ ] ✅ Concrete examples provided
- [ ] ✅ Edge cases handled
- [ ] ✅ Dependencies listed (if any)
- [ ] ✅ Level 1/2/3 structure followed
- [ ] ✅ Links to reference files work

### Chi tiết đầy đủ:
Xem [validation.md](./validation.md) cho comprehensive checklist.

---

## Step 9: Test Skill

### Testing Steps:

**1. Restart AI AGENT**
```bash
# Restart to load new skill
```

**2. Hỏi câu liên quan**
- Match với description triggers
- Use actual user phrases
- Test both Vietnamese và English

**3. Verify activation**
- AI AGENT phải tự động dùng skill
- Check skill loads correctly
- Confirm right skill activates (no conflicts)

**4. Check behavior**
- Confirm AI AGENT follows instructions correctly
- Test examples work as documented
- Verify outputs match expectations

### Test Scenarios:

**Scenario 1: Direct trigger**
```
User: "tạo skill mới"
Expected: create-skill activates
```

**Scenario 2: Contextual trigger**
```
User: "I need help with SKILL.md frontmatter"
Expected: create-skill activates
```

**Scenario 3: File type trigger**
```
User: "Process this PDF document"
Expected: pdf-processor activates (if exists)
```

---

## Step 10: Debug nếu cần

### Nếu AI AGENT không dùng skill:

**1. Make description more specific**
- Add trigger words
- Include file types và operations
- Add "Use when..." clause with user phrases

**2. Check file location**
```bash
ls ~/.claude/skills/{group-skill-name}/{skill-name}/SKILL.md
```

**3. Validate YAML syntax**
```bash
cat SKILL.md | head -n 10
# Check for tabs, indentation, quotes
```

**4. Run debug mode**
```bash
AI AGENT --debug
```

### Common Debug Issues:

| Issue | Solution |
|-------|----------|
| Skill không load | Check file path và permissions |
| YAML parse error | Verify frontmatter syntax |
| Wrong skill activates | Make triggers more specific |
| Description không match | Add more trigger words |

---

## Step 11: Đăng ký Skill vào active_skill.md

### QUAN TRỌNG:
Sau khi tạo hoặc cập nhật skill, **phải đăng ký** vào:
```
E:\Laravel\study\skill_system\.claude\hook\choose_skill.md\active_skill.md
```

### Format đăng ký:

```markdown
## Skill {skill-name}:
- Đường dẫn: \.claude\skills\{group-skill-name}\{skill-name}
- Mô tả: {Full description from frontmatter}
- Lời gọi kích hoạt:
  - "trigger word 1"
  - "trigger word 2"
  - "trigger word 3"
  - "trigger word 4"
  - "trigger word 5"
  ...
```

### Trigger words guidelines:

**Extract từ description field**:
- Include **file extensions** (.pdf, .xlsx, .json)
- Include **operations** (analyze, extract, generate, create, tạo, xử lý)
- Include **domain terms** (API, database, UI/UX, git)
- **Both Vietnamese và English** terms
- **User phrases** ("tạo skill", "create skill", "viết skill")

**Aim for 5-10 triggers**:
- Minimum 5 triggers
- Ideal 7-10 triggers
- Cover variations và common phrasings

### Khi nào cập nhật active_skill.md:

1. ✅ **Tạo skill mới** → Add new section
2. ✅ **Thay đổi description** → Update Mô tả
3. ✅ **Thêm trigger words** → Update Lời gọi kích hoạt
4. ✅ **Đổi tên skill** → Update section name và đường dẫn
5. ✅ **Move skill sang group khác** → Update đường dẫn
6. ✅ **Deprecate skill** → Remove section

### Example Registration:

```markdown
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
```

### Chi tiết đầy đủ:
Xem [registration.md](./registration.md) cho comprehensive guide về skill registration.

---

## Summary

Workflow 11 bước hoàn chỉnh:

1. ✅ **Xác định phạm vi** - One skill, one capability
2. ✅ **Chọn location** - Group skill phù hợp
3. ✅ **Tạo structure** - Folders và required files
4. ✅ **Viết frontmatter** - name, description chuẩn
5. ✅ **Viết description** - Specific, triggers, context
6. ✅ **Cấu trúc nội dung** - Level 1/2/3, <200 dòng
7. ✅ **Supporting files** - README, reference/, assets/, scripts/
8. ✅ **Validate** - YAML, structure, content
9. ✅ **Test** - Activation, behavior, examples
10. ✅ **Debug** - Fix issues if any
11. ✅ **Đăng ký** - active_skill.md với triggers

Kết quả: Một skill hoàn chỉnh, working, đã đăng ký, sẵn sàng sử dụng! 🚀
