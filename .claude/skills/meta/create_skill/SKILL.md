---
name: create-skill
description: Tạo Agent Skills mới cho AI AGENT theo đúng chuẩn và best practices. Dùng khi người dùng muốn tạo, viết, thiết kế skill mới, hoặc cần trợ giúp với file SKILL.md, frontmatter, hoặc cấu trúc skill. Trigger words: tạo skill, viết skill, create skill, new skill, SKILL.md, skill structure.
---

# Create Skill

## Level 1: Overview (Always Read First)

Skill này giúp bạn tạo ra các Agent Skills có cấu trúc tốt cho AI AGENT, tuân thủ các best practices và yêu cầu validation. Skill này là meta skill - dùng để tạo ra các skill khác.

## Prerequisites

- Quyền truy cập thư mục `.claude/skills/`
- Hiểu rõ mục đích và phạm vi của skill cần tạo
- Biết group skill phù hợp (tham khảo `.claude/hook/hook_group_skill/choose_group_skill.md`)

## What This Skill Does

1. Hướng dẫn xác định phạm vi và mục đích của skill (One Skill = One Capability)
2. Tạo cấu trúc folder và file theo đúng chuẩn (SKILL.md <200 dòng, README.md <100 ký tự)
3. Viết SKILL.md với frontmatter hợp lệ (name, description với trigger words)
4. Tạo các file hỗ trợ (README, reference/, assets/, scripts/)
5. Validate và test skill vừa tạo
6. Đăng ký skill vào active_skill.md với trigger words

---

## Level 2: Quick Start (For Fast Onboarding)

### Basic Workflow

```bash
# 1. Xác định group skill và tạo structure
mkdir -p ~/.claude/skills/{group-skill-name}/{skill-name}/reference

# 2. Tạo SKILL.md với frontmatter (name, description)
# 3. Tạo README.md (<100 ký tự)
# 4. Tạo reference files nếu cần (workflow, examples, validation)
# 5. Validate YAML, structure, content
# 6. Test activation với trigger words
# 7. Đăng ký vào active_skill.md
```

### Key Principles

- **One Skill = One Capability** - Focused, không tạo mega-skills
- **SKILL.md < 200 dòng** - Content chính ngắn gọn, details trong reference/
- **README.md < 100 ký tự** - Mô tả human-readable
- **Reference files < 300 dòng** - Mỗi file 1 tính năng
- **Trigger words** - Both Vietnamese và English, 5-10 triggers
- **Register immediately** - Đăng ký vào active_skill.md ngay sau khi tạo

---

## Level 3: Reference Guides

- [workflow.md](./reference/workflow.md) - Use when: Cần 11 steps chi tiết để tạo skill
- [validation.md](./reference/validation.md) - Use when: Cần kiểm tra skill có đúng chuẩn không
- [examples.md](./reference/examples.md) - Use when: Cần tham khảo ví dụ skill mẫu
- [registration.md](./reference/registration.md) - Use when: Cần đăng ký skill vào active_skill.md hoặc update registration

---

## Folder Structure Template

```
~/.claude/skills/{group-skill-name}/{skill-name}/
├── SKILL.md         # REQUIRED: <200 dòng
├── README.md        # REQUIRED: <100 ký tự
├── reference/       # Optional: Chi tiết
│   ├── workflow.md
│   ├── examples.md
│   └── features.md  # Max 300 dòng, >100 dòng thêm TOC
├── assets/          # Optional: Templates, icons
└── scripts/         # Optional: Executable scripts
```

---

## SKILL.md Frontmatter Template

```yaml
---
name: skill-name                    # lowercase, hyphens, max 64 chars
description: What it does + When to use it + Trigger words
allowed-tools: Read, Grep, Glob    # Optional: restrict tools
---

# Skill Name

## Level 1: Overview
Brief 2-3 sentence description.

## Prerequisites
- Requirements

## What This Skill Does
1. Function 1
2. Function 2

---

## Level 2: Quick Start

### Basic Usage
# Commands or examples

### Key Steps
1. Step 1
2. Step 2

---

## Level 3: Reference Guides
- [guide.md](./reference/guide.md) - Use when: context

---

## Common Use Cases
- Case 1
- Case 2

## Best Practices
- Practice 1
- Practice 2

## Troubleshooting
| Issue | Solution |
|-------|----------|
| Problem | Fix |
```

---

## Description Writing Formula

```
[What it does] + [When to use it] + [Key triggers]
```

**Good Example**:
```yaml
description: Extract text and tables from PDF files, fill forms, merge documents. Use when working with PDF files or when the user mentions PDFs, forms, or document extraction.
```

**Include**:
- File extensions (.pdf, .xlsx, .json)
- Operations (analyze, extract, generate, create)
- Domain terms (API, database, UI/UX)
- User phrases (both Vietnamese và English)

---

## Registration Format (active_skill.md)

```markdown
## Skill {skill-name}:
- Đường dẫn: \.claude\skills\{group}\{skill-name}
- Mô tả: {Copy from SKILL.md description}
- Lời gọi kích hoạt:
  - "trigger 1"
  - "trigger 2"
  - "trigger 3"
  - "trigger 4"
  - "trigger 5"
```

**Update khi**:
- ✅ Tạo skill mới
- ✅ Thay đổi description
- ✅ Thêm trigger words
- ✅ Đổi tên hoặc move skill

---

## Common Use Cases

### Case 1: Tạo Simple Single-File Skill
- Tất cả trong SKILL.md (<200 dòng)
- README.md với description ngắn
- Đăng ký vào active_skill.md với 5-7 triggers

### Case 2: Tạo Read-Only Skill
- Add `allowed-tools: Read, Grep, Glob` trong frontmatter
- For code review, security audit, analysis
- No Edit, Create, Execute tools

### Case 3: Tạo Multi-File Skill
- SKILL.md (<200 dòng) với overview và quick start
- reference/ chứa detailed guides
- Progressive disclosure: workflow.md, examples.md, validation.md

---

## Best Practices

1. **One Skill, one purpose** - Focused skills activate reliably
2. **SKILL.md < 200 dòng** - Move details to reference/
3. **Specific descriptions** - Include trigger words users will say
4. **Clear instructions** - Viết cho AI AGENT, not humans
5. **Register immediately** - Đăng ký vào active_skill.md NGAY sau khi tạo
6. **Extract good triggers** - 5-10 trigger words, both VN và EN
7. **Test activation** - Test với trigger words sau khi register

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Skill không activate | **Check đã register trong active_skill.md chưa**; Add specific trigger words; Include file types và operations |
| SKILL.md quá dài | Move Step-by-Step instructions sang workflow.md; Move examples sang examples.md; Keep only essentials |
| Description quá vague | Add file extensions (.pdf, .xlsx); Add operations (create, analyze); Add "Use when..." clause |
| Trigger words không work | Verify format (2 spaces indent, quotes); Add both VN và EN; Include file types |
| Wrong skill activates | Make triggers more specific; Review overlap với other skills; Update description |

---

## Output Format

Khi tạo một Skill, tôi sẽ:

1. ✅ Hỏi các câu làm rõ về scope và requirements
2. ✅ Suggest skill name và location (group skill)
3. ✅ Tạo SKILL.md với proper frontmatter (<200 dòng)
4. ✅ Include clear instructions và examples
5. ✅ Add supporting files nếu cần (README, reference/, assets/, scripts/)
6. ✅ **Đăng ký skill vào active_skill.md với trigger words**
7. ✅ Validate against all requirements (YAML, structure, content)
8. ✅ Test skill activation với trigger words

Kết quả: một Skill hoàn chỉnh, working, đã đăng ký trong active_skill.md, tuân thủ tất cả best practices và validation rules.
