# Skill Registration Guide

Hướng dẫn chi tiết về đăng ký và quản lý skills trong `active_skill.md`.

## Table of Contents

- [Overview](#overview)
- [Registration Format](#registration-format)
- [Extracting Trigger Words](#extracting-trigger-words)
- [Registration Examples](#registration-examples)
- [Update Scenarios](#update-scenarios)
- [Troubleshooting](#troubleshooting)

---

## Overview

File `E:\Laravel\study\skill_system\.claude\hook\choose_skill.md\active_skill.md` là registry tập trung của tất cả skills. AI AGENT sử dụng file này để:

1. **Discover skills** - Tìm skill phù hợp với user query
2. **Match triggers** - So sánh user input với trigger words
3. **Load skills** - Activate đúng skill based on context

**Khi nào phải đăng ký**:
- ✅ Tạo skill mới
- ✅ Update description
- ✅ Thay đổi trigger words
- ✅ Đổi tên hoặc move skill
- ✅ Thêm/bớt features chính

---

## Registration Format

### Template Chuẩn

```markdown
## Skill {skill-name}:
- Đường dẫn: \.claude\skills\{group-skill-name}\{skill-name}
- Mô tả: {Full description from SKILL.md frontmatter}
- Lời gọi kích hoạt:
  - "trigger word 1"
  - "trigger word 2"
  - "trigger word 3"
  - "trigger word 4"
  - "trigger word 5"
```

### Field Requirements

#### Skill Name
- Phải match với `name` field trong SKILL.md frontmatter
- Lowercase, hyphens only
- Format: `## Skill {skill-name}:`

#### Đường dẫn
- Relative path từ `.claude/`
- Format: `\.claude\skills\{group-skill-name}\{skill-name}`
- Dùng backslashes (`\`) như trong example

#### Mô tả
- Copy NGUYÊN VĂN từ `description` field trong SKILL.md
- Bao gồm cả "what it does" và "when to use it"
- Không rút gọn, không paraphrase

#### Lời gọi kích hoạt
- Mỗi trigger một dòng với format: `  - "trigger"`
- Có 2 spaces indent trước dash
- Wrap trigger trong quotes
- Minimum 3-5 triggers, recommend 5-10 triggers
- Both Vietnamese và English

---

## Extracting Trigger Words

### Step-by-Step Process

1. **Read description field** từ SKILL.md frontmatter
2. **Identify key terms**:
   - File types (.pdf, .xlsx, .csv, .json)
   - Operations (create, analyze, extract, transform, generate)
   - Domain terms (API, database, UI/UX, git, commit)
   - User phrases đã mention trong description

3. **Expand to Vietnamese**:
   - Translate key English terms
   - Add common Vietnamese phrases
   - Include both formal and casual terms

4. **Add variations**:
   - Singular/plural forms
   - Short/long forms
   - Abbreviations and full terms

### Trigger Word Categories

#### 1. Direct Operations
```
"create skill"
"tạo skill"
"new skill"
"skill mới"
"write skill"
"viết skill"
```

#### 2. File Types
```
"PDF"
"Excel"
".xlsx"
".csv"
"JSON files"
```

#### 3. Domain Terms
```
"REST API"
"API design"
"git commit"
"database"
"UI/UX"
```

#### 4. User Intent Phrases
```
"help me with..."
"I need to..."
"how do I..."
"làm thế nào để..."
"giúp tôi..."
```

#### 5. Feature-Specific
```
"form filling"
"data extraction"
"code review"
"merge JSON"
```

---

## Registration Examples

### Example 1: Simple Skill

**SKILL.md frontmatter**:
```yaml
---
name: commit-helper
description: Generate well-formatted git commit messages following conventional commits standard. Use when creating commits, writing commit messages, or when user says "git commit", "commit message", or needs help with commit formatting.
---
```

**Registration trong active_skill.md**:
```markdown
## Skill commit-helper:
- Đường dẫn: \.claude\skills\git\commit-helper
- Mô tả: Generate well-formatted git commit messages following conventional commits standard. Use when creating commits, writing commit messages, or when user says "git commit", "commit message", or needs help with commit formatting.
- Lời gọi kích hoạt:
  - "git commit"
  - "commit message"
  - "tạo commit"
  - "viết commit message"
  - "conventional commits"
  - "commit formatting"
  - "format commit"
```

### Example 2: File Processing Skill

**SKILL.md frontmatter**:
```yaml
---
name: pdf-processor
description: Extract text and tables from PDF files, fill forms, merge documents. Use when working with PDF files or when the user mentions PDFs, forms, or document extraction.
---
```

**Registration**:
```markdown
## Skill pdf-processor:
- Đường dẫn: \.claude\skills\documents\pdf-processor
- Mô tả: Extract text and tables from PDF files, fill forms, merge documents. Use when working with PDF files or when the user mentions PDFs, forms, or document extraction.
- Lời gọi kích hoạt:
  - "PDF"
  - "PDF files"
  - "extract PDF"
  - "extract text from PDF"
  - "PDF forms"
  - "fill PDF form"
  - "merge PDFs"
  - "xử lý PDF"
  - "trích xuất PDF"
  - "form PDF"
```

### Example 3: Meta Skill (create-skill)

**SKILL.md frontmatter**:
```yaml
---
name: create-skill
description: Tạo Agent Skills mới cho AI AGENT theo đúng chuẩn và best practices. Dùng khi người dùng muốn tạo, viết, thiết kế skill mới, hoặc cần trợ giúp với file SKILL.md, frontmatter, hoặc cấu trúc skill. Trigger words: tạo skill, viết skill, create skill, new skill, SKILL.md, skill structure.
---
```

**Registration**:
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
```

### Example 4: Data Processing Skill

**SKILL.md frontmatter**:
```yaml
---
name: json-processor
description: Process, merge, filter, and transform JSON files with advanced operations. Use when working with JSON data, data merging, filtering, or user mentions JSON processing, data transformation, or JSON manipulation.
---
```

**Registration**:
```markdown
## Skill json-processor:
- Đường dẫn: \.claude\skills\data\json-processor
- Mô tả: Process, merge, filter, and transform JSON files with advanced operations. Use when working with JSON data, data merging, filtering, or user mentions JSON processing, data transformation, or JSON manipulation.
- Lời gọi kích hoạt:
  - "JSON"
  - "JSON files"
  - "process JSON"
  - "merge JSON"
  - "filter JSON"
  - "transform JSON"
  - "JSON processing"
  - "JSON manipulation"
  - "xử lý JSON"
  - "merge dữ liệu JSON"
  - "filter data JSON"
  - "chuyển đổi JSON"
```

---

## Update Scenarios

### Scenario 1: Tạo Skill Mới

**Action**: Thêm section mới vào cuối file `active_skill.md`

**Steps**:
1. Open `E:\Laravel\study\skill_system\.claude\hook\choose_skill.md\active_skill.md`
2. Scroll to bottom
3. Add new section với format chuẩn
4. Extract triggers từ description
5. Add 5-10 trigger words (both VN and EN)
6. Save file

**Example**:
```markdown
## Skill my-new-skill:
- Đường dẫn: \.claude\skills\category\my-new-skill
- Mô tả: [Copy from SKILL.md]
- Lời gọi kích hoạt:
  - "trigger 1"
  - "trigger 2"
  - ...
```

### Scenario 2: Update Description

**Action**: Update section "Mô tả" trong active_skill.md

**Steps**:
1. Find skill section trong active_skill.md
2. Locate `- Mô tả:` line
3. Replace với new description từ SKILL.md
4. Review trigger words - có thể cần update
5. Save file

**Before**:
```markdown
## Skill my-skill:
- Đường dẫn: \.claude\skills\category\my-skill
- Mô tả: Old description here
- Lời gọi kích hoạt:
  ...
```

**After**:
```markdown
## Skill my-skill:
- Đường dẫn: \.claude\skills\category\my-skill
- Mô tả: New updated description with more details and context
- Lời gọi kích hoạt:
  ...
```

### Scenario 3: Thêm Trigger Words

**Action**: Add new triggers vào "Lời gọi kích hoạt"

**Steps**:
1. Find skill section
2. Locate `- Lời gọi kích hoạt:` section
3. Add new triggers keeping format:
   - 2 spaces indent
   - Dash and space: `- `
   - Quotes around trigger: `"trigger"`
4. Maintain alphabetical or logical order
5. Save file

**Before**:
```markdown
- Lời gọi kích hoạt:
  - "create API"
  - "API design"
  - "REST API"
```

**After**:
```markdown
- Lời gọi kích hoạt:
  - "create API"
  - "API design"
  - "REST API"
  - "thiết kế API"
  - "tạo API"
  - "API endpoint"
```

### Scenario 4: Đổi Tên Skill

**Action**: Update skill name và đường dẫn

**Steps**:
1. Update `## Skill {new-name}:` line
2. Update `- Đường dẫn:` với new path
3. Ensure name matches SKILL.md frontmatter
4. Trigger words có thể cần update
5. Save file

**Before**:
```markdown
## Skill old-name:
- Đường dẫn: \.claude\skills\category\old-name
```

**After**:
```markdown
## Skill new-name:
- Đường dẫn: \.claude\skills\category\new-name
```

### Scenario 5: Move Skill to Different Group

**Action**: Update đường dẫn với new group

**Steps**:
1. Find skill section
2. Update `- Đường dẫn:` với new group-skill-name
3. Verify physical folder đã moved
4. Test skill activation
5. Save file

**Before**:
```markdown
## Skill my-skill:
- Đường dẫn: \.claude\skills\old-group\my-skill
```

**After**:
```markdown
## Skill my-skill:
- Đường dẫn: \.claude\skills\new-group\my-skill
```

### Scenario 6: Remove/Deprecate Skill

**Action**: Delete section từ active_skill.md

**Steps**:
1. Find skill section to remove
2. Delete entire section (from `## Skill` to last trigger)
3. Save file
4. Consider leaving comment if needed:
   ```markdown
   <!-- Removed: my-old-skill - deprecated on 2025-11-14 -->
   ```

---

## Troubleshooting

### Issue 1: Skill Không Activate

**Symptoms**: User query matches skill nhưng AI AGENT không load

**Solutions**:
1. ✅ Check skill đã registered trong active_skill.md
2. ✅ Verify trigger words match user query
3. ✅ Ensure "Mô tả" copied correctly từ SKILL.md
4. ✅ Add more specific trigger words
5. ✅ Check file format (2 spaces indent, quotes around triggers)

### Issue 2: Wrong Skill Activates

**Symptoms**: Skill khác activate thay vì skill mong muốn

**Solutions**:
1. ✅ Make trigger words more specific
2. ✅ Review overlapping triggers with other skills
3. ✅ Update description to be more distinct
4. ✅ Add domain-specific terms to triggers
5. ✅ Remove generic triggers

### Issue 3: Format Errors

**Symptoms**: Parse errors, skill không load correctly

**Solutions**:
1. ✅ Verify indentation (2 spaces before dash)
2. ✅ Check quotes around triggers: `"trigger"`
3. ✅ Ensure backslashes in path: `\.claude\skills\`
4. ✅ Validate Markdown syntax
5. ✅ Check for special characters trong triggers

### Issue 4: Description Too Long

**Symptoms**: Section trông cluttered

**Solutions**:
1. ✅ Copy NGUYÊN VĂN from SKILL.md (không rút gọn)
2. ✅ Nếu description dài, consider line breaks
3. ✅ Đảm bảo description trong SKILL.md < 1024 chars
4. ✅ Không paraphrase - consistency là quan trọng

### Issue 5: Missing Triggers

**Symptoms**: Skill không activate với common queries

**Solutions**:
1. ✅ Add both Vietnamese và English triggers
2. ✅ Include file extensions (.pdf, .json, .xlsx)
3. ✅ Add operation verbs (create, analyze, extract)
4. ✅ Include domain terms (API, database, git)
5. ✅ Add user intent phrases ("help me", "how do I")

---

## Best Practices

### 1. Consistency
- Always copy description from SKILL.md (không edit)
- Use exact same format cho tất cả skills
- Maintain alphabetical order (optional but recommended)

### 2. Trigger Words
- Minimum 5 triggers, ideal 7-10 triggers
- Balance between specific và broad
- Include both languages (VN + EN)
- Cover different phrasings của same concept

### 3. Documentation
- Update active_skill.md IMMEDIATELY after changing skill
- Keep descriptions in sync với SKILL.md
- Document major changes trong git commits

### 4. Testing
- Test skill activation sau khi register
- Try different trigger phrases
- Verify không conflict với other skills
- Get feedback from team members

### 5. Maintenance
- Regular review của trigger words
- Remove deprecated skills promptly
- Update paths khi restructure folders
- Keep file organized và readable

---

## Quick Checklist

Sau khi register hoặc update skill trong active_skill.md:

- [ ] ✅ Skill name matches SKILL.md frontmatter
- [ ] ✅ Đường dẫn correct và uses backslashes
- [ ] ✅ Mô tả copied NGUYÊN VĂN từ SKILL.md
- [ ] ✅ At least 5 trigger words added
- [ ] ✅ Triggers include both Vietnamese và English
- [ ] ✅ Format correct (2 spaces indent, quotes)
- [ ] ✅ No syntax errors
- [ ] ✅ Tested skill activation
- [ ] ✅ No conflicts với other skills
- [ ] ✅ File saved properly

---

## Template for Quick Copy

```markdown
## Skill {skill-name}:
- Đường dẫn: \.claude\skills\{group}\{skill-name}
- Mô tả: {Copy from SKILL.md description field}
- Lời gọi kích hoạt:
  - "trigger 1"
  - "trigger 2"
  - "trigger 3"
  - "trigger 4"
  - "trigger 5"
  - "trigger 6"
  - "trigger 7"

```

Replace placeholders và fill in actual values.
