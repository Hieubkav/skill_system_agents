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
2. **Check duplicate skills** - Phát hiện skills trùng/tương tự và đưa ra giải pháp (merge/delete/refine)
3. Tạo cấu trúc folder và file theo đúng chuẩn (SKILL.md <200 dòng, README.md <100 ký tự)
4. Viết SKILL.md với frontmatter hợp lệ (name, description với trigger words)
5. Tạo các file hỗ trợ (README, reference/, assets/, scripts/)
6. Validate và test skill vừa tạo
7. Đăng ký skill vào active_skill.md với trigger words

---

## Level 2: Quick Start (For Fast Onboarding)

### Basic Workflow

```bash
# 1. Xác định scope skill (name, description, purpose)
# 2. CHECK DUPLICATE (NEW!) - Compare với skills hiện có trong active_skill.md
#    → Nếu HIGH/MEDIUM similarity: Merge/Delete/Refine/Keep-Both/Cancel
#    → Nếu LOW similarity: Proceed
# 3. Xác định group skill và tạo structure
mkdir -p ~/.claude/skills/{group-skill-name}/{skill-name}/references

# 4. Tạo SKILL.md với frontmatter (name, description)
# 5. Tạo README.md (<100 ký tự)
# 6. Tạo references files nếu cần (workflow, examples, validation)
# 7. Validate YAML, structure, content
# 8. Test activation với trigger words
# 9. Đăng ký vào active_skill.md
# 10. AUTO-UPDATE INDEX (CRITICAL!) - Regenerate skill_index.md và skills/ folder
python .claude/skills/meta/create_skill/scripts/generate_skill_index.py
```

### Key Principles

- **One Skill = One Capability** - Focused, không tạo mega-skills
- **Check Duplicate First** - Tránh tạo skills trùng lặp, prefer merge/refine existing skills
- **SKILL.md < 200 dòng** - Content chính ngắn gọn, details trong references/
- **README.md < 100 ký tự** - Mô tả human-readable, và dùng tiếng Việt
- **References files < 300 dòng** - Mỗi file 1 tính năng
- **Trigger words** - Both Vietnamese và English, 5-10 triggers
- **Register immediately** - Đăng ký vào active_skill.md ngay sau khi tạo
- **Auto-update index** - Chạy generate_skill_index.py sau mỗi skill change (CRITICAL!)

---

## Level 3: Reference Guides

- [workflow.md](./references/workflow.md) - Use when: Cần 12 steps chi tiết để tạo skill (bao gồm duplicate check)
- [duplicate-detection.md](./references/duplicate-detection.md) - Use when: Cần check duplicate skills, merge/refine existing skills
- [validation.md](./references/validation.md) - Use when: Cần kiểm tra skill có đúng chuẩn không
- [examples.md](./references/examples.md) - Use when: Cần tham khảo ví dụ skill mẫu
- [registration.md](./references/registration.md) - Use when: Cần đăng ký skill vào active_skill.md hoặc update registration

---

## Folder Structure Template

```
~/.claude/skills/{group-skill-name}/{skill-name}/
├── SKILL.md         # REQUIRED: <200 dòng
├── README.md        # REQUIRED: <100 ký tự
├── references/      # Optional: Chi tiết
│   ├── workflow.md
│   ├── examples.md
│   └── features.md  # Max 300 dòng, >100 dòng thêm TOC
├── assets/          # Optional: Templates, icons
└── scripts/         # Optional: Executable scripts
```

---

## SKILL.md Template (Simplified)

Template chuẩn với 3 levels: Overview, Quick Start, References. Chi tiết xem [workflow.md](./references/workflow.md).

**Frontmatter**:
```yaml
---
name: skill-name  # lowercase, hyphens, max 64 chars
description: What it does + When to use it + Trigger words
---
```

**Description Formula**: `[What it does] + [When to use it] + [Key triggers]`

**Registration**: Copy description và triggers vào `active_skill.md`. Xem [registration.md](./references/registration.md).

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
- references/ chứa detailed guides
- Progressive disclosure: workflow.md, examples.md, validation.md

---

## Best Practices

1. **One Skill, one purpose** - Focused skills activate reliably
2. **SKILL.md < 200 dòng** - Move details to references/
3. **Specific descriptions** - Include trigger words users will say
4. **Clear instructions** - Viết cho AI AGENT, not humans
5. **Register immediately** - Đăng ký vào active_skill.md NGAY sau khi tạo
6. **Extract good triggers** - 5-10 trigger words, both VN và EN
7. **Test activation** - Test với trigger words sau khi register

---

## Utility Scripts

Skill này includes automation scripts trong `scripts/` folder:

- **list_skills.py** - List tất cả skills đã đăng ký
- **update_active_skills.py** - Batch update active_skill.md (remove old, add new skills)
- **cleanup_duplicates.py** - Remove duplicate skill registrations

Xem [scripts/README.md](./scripts/README.md) cho chi tiết usage.

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Skill không activate | **Check đã register trong active_skill.md chưa**; Add specific trigger words; Include file types và operations |
| SKILL.md quá dài | Move Step-by-Step instructions sang workflow.md; Move examples sang examples.md; Keep only essentials |
| Description quá vague | Add file extensions (.pdf, .xlsx); Add operations (create, analyze); Add "Use when..." clause |
| Trigger words không work | Verify format (2 spaces indent, quotes); Add both VN và EN; Include file types |
| Wrong skill activates | Make triggers more specific; Review overlap với other skills; Update description |
| Duplicate skills | Run `scripts/cleanup_duplicates.py` để remove duplicates |

---

## Output Format

Khi tạo một Skill, tôi sẽ:

1. ✅ Hỏi các câu làm rõ về scope và requirements
2. ✅ **Check duplicate skills trong active_skill.md** - Đưa ra warnings và recommendations nếu phát hiện trùng
3. ✅ Suggest skill name và location (group skill)
4. ✅ Tạo SKILL.md với proper frontmatter (<200 dòng)
5. ✅ Include clear instructions và examples
6. ✅ Add supporting files nếu cần (README, references/, assets/, scripts/)
7. ✅ **Đăng ký skill vào active_skill.md với trigger words**
8. ✅ Validate against all requirements (YAML, structure, content)
9. ✅ Test skill activation với trigger words
10. ✅ **AUTO-UPDATE INDEX** - Chạy `generate_skill_index.py` để sync skill_index.md và skills/ folder

**CRITICAL**: Sau mỗi skill change (create/update/merge/delete), PHẢI chạy:
```bash
python .claude/skills/meta/create_skill/scripts/generate_skill_index.py
```

Kết quả: một Skill hoàn chỉnh, working, đã đăng ký trong active_skill.md, index đã sync, không duplicate, tuân thủ tất cả best practices và validation rules.
