# Validation Checklist

Checklist đầy đủ để validate một Agent Skill trước khi hoàn thành.

## 1. File Structure Validation

### Required Files
- [ ] **SKILL.md** exists trong đúng location
- [ ] **README.md** exists
- [ ] Directory name matches frontmatter `name` field

### Optional Files (nếu có)
- [ ] **reference/** directory cho docs chi tiết
- [ ] **assets/** directory cho templates, icons, fonts
- [ ] **scripts/** directory cho executable scripts

### File Organization
- [ ] Mỗi reference file tập trung vào 1 tính năng
- [ ] Reference files ≤ 300 dòng
- [ ] Reference files > 100 dòng có TOC (Table of Contents)
- [ ] README.md < 100 ký tự và là tiếng việt 

---

## 2. YAML Frontmatter Validation

### Structure
- [ ] Opening `---` nằm trên line 1
- [ ] Closing `---` nằm trước content
- [ ] Valid YAML syntax (no tabs, đúng indentation)
- [ ] Không có trailing spaces

### Required Fields
- [ ] **name** field exists
- [ ] **description** field exists

### Name Field Rules
- [ ] Chỉ chứa: lowercase letters (a-z), numbers (0-9), hyphens (-)
- [ ] Không chứa: UPPERCASE, underscores (_), spaces, special chars
- [ ] Length ≤ 64 characters
- [ ] Name khớp chính xác với directory name
- [ ] Không bắt đầu hoặc kết thúc bằng hyphen

**Examples**:
- ✅ `pdf-processor`
- ✅ `git-commit-helper`
- ✅ `api-v2-designer`
- ❌ `PDF_Processor` (uppercase, underscore)
- ❌ `Git Commits!` (spaces, special chars)
- ❌ `skill_name` (underscore)

### Description Field Rules
- [ ] Length ≤ 1024 characters
- [ ] Bao gồm **what it does** (chức năng chính)
- [ ] Bao gồm **when to use it** (khi nào dùng)
- [ ] Có trigger words mà users sẽ nói
- [ ] Đề cập file types nếu relevant (.pdf, .xlsx, .json, etc.)
- [ ] Đề cập operations cụ thể (analyze, extract, generate, etc.)
- [ ] Add context clues ("Use when...", "For...", etc.)

**Description Formula**:
```
[What it does] + [When to use it] + [Key triggers]
```

**Good Examples**:
```yaml
description: Extract text and tables from PDF files, fill forms, merge documents. Use when working with PDF files or when the user mentions PDFs, forms, or document extraction.
```

```yaml
description: Analyze Excel spreadsheets, create pivot tables, and generate charts. Use when working with Excel files, spreadsheets, or analyzing tabular data in .xlsx format.
```

**Bad Examples**:
```yaml
description: Helps with documents  # Quá vague
description: For data analysis     # Thiếu details
description: Process files         # Không có context
```

### Optional Fields
- [ ] **allowed-tools** (nếu có): Comma-separated list of tool names
  - Valid format: `Read, Grep, Glob`
  - Dùng khi: Read-only skills, security-sensitive workflows

---

## 3. Content Quality Validation

### Instructions
- [ ] Clear step-by-step instructions for AI AGENT
- [ ] Written for AI, not humans
- [ ] Specific actions, not vague suggestions
- [ ] Concrete examples provided (real code, not pseudocode)
- [ ] Edge cases handled
- [ ] Error handling mentioned

### Examples
- [ ] Có ít nhất 1 example cụ thể
- [ ] Examples sử dụng real code/commands
- [ ] Examples realistic và practical
- [ ] Examples cover common use cases

### Documentation Structure
- [ ] **Level 1: Overview** - Brief 2-3 sentence description
- [ ] **Prerequisites** - Required tools, permissions, knowledge
- [ ] **What This Skill Does** - Numbered list of capabilities
- [ ] **Level 2: Quick Start** - Basic usage và key steps
- [ ] **Level 3: Reference Guides** - Links to detailed docs
- [ ] **Common Use Cases** - Specific scenarios
- [ ] **Best Practices** - Do's and don'ts
- [ ] **Troubleshooting** - Common issues và solutions

### Dependencies
- [ ] All dependencies listed in Prerequisites
- [ ] Required packages mentioned in description (nếu có)
- [ ] Tool requirements specified (nếu có allowed-tools)
- [ ] External services documented (APIs, databases, etc.)

### File Paths
- [ ] Sử dụng forward slashes (/) not backslashes (\)
- [ ] Paths relative to skill directory
- [ ] Links to reference files correct
- [ ] Asset paths valid

---

## 4. Content Formatting Validation

### Markdown
- [ ] Valid Markdown syntax
- [ ] Headers follow hierarchy (H1 > H2 > H3)
- [ ] Code blocks có language specified
- [ ] Lists properly formatted
- [ ] Tables có proper structure (nếu có)

### Code Blocks
- [ ] Language identifier specified (\`\`\`bash, \`\`\`python, etc.)
- [ ] Code runnable/valid
- [ ] Commands include necessary flags
- [ ] Comments explain non-obvious parts

### Links
- [ ] Internal links use relative paths
- [ ] Links to reference files correct
- [ ] External links valid (nếu có)
- [ ] Link text descriptive

---

## 5. Functionality Validation

### Skill Discovery
- [ ] Description contains trigger words users would say
- [ ] Specific enough để distinguish from other skills
- [ ] Not too broad or generic
- [ ] File types và operations mentioned

### Testing Checklist
- [ ] Skill activates on relevant queries
- [ ] AI AGENT follows instructions correctly
- [ ] Examples work as documented
- [ ] Scripts execute successfully (nếu có)
- [ ] No conflicts with other skills

### Test Scenarios
1. **Activation Test**:
   - [ ] Hỏi câu matching description
   - [ ] Verify AI AGENT loads skill automatically

2. **Instruction Test**:
   - [ ] Follow skill instructions step-by-step
   - [ ] Confirm expected behavior

3. **Example Test**:
   - [ ] Run examples from documentation
   - [ ] Verify outputs correct

4. **Edge Case Test**:
   - [ ] Test with invalid inputs
   - [ ] Verify error handling

---

## 6. Security Validation

### Permissions
- [ ] `allowed-tools` restrictive nếu cần
- [ ] Read-only skills không có Write/Edit/Execute
- [ ] No unnecessary tool permissions

### Sensitive Data
- [ ] No hardcoded credentials
- [ ] No API keys in code
- [ ] No sensitive paths exposed
- [ ] Proper input validation in scripts

### Scripts (nếu có)
- [ ] Scripts have proper shebang
- [ ] Execute permissions set correctly
- [ ] No arbitrary code execution
- [ ] Input validation implemented

---

## 7. Maintenance Validation

### Documentation
- [ ] README.md clear và concise
- [ ] Change log mentioned (nếu có versions)
- [ ] Author/maintainer info (optional)
- [ ] License info (optional for team skills)

### Versioning
- [ ] Document breaking changes
- [ ] Update description if behavior changes
- [ ] Keep backwards compatibility if possible

### Team Skills (nếu share với team)
- [ ] Tested by multiple people
- [ ] Instructions clear cho all team members
- [ ] No personal-specific paths or configs
- [ ] Documentation complete

---

## 8. Final Pre-Release Checklist

Trước khi hoàn thành skill:

- [ ] ✅ All validation checks above passed
- [ ] ✅ Tested with real queries
- [ ] ✅ No errors in logs
- [ ] ✅ Documentation complete
- [ ] ✅ Examples verified
- [ ] ✅ YAML valid
- [ ] ✅ Naming conventions followed
- [ ] ✅ File structure correct
- [ ] ✅ Ready for use/sharing

---

## Common Validation Errors

| Error | Solution |
|-------|----------|
| Skill không activate | Add trigger words to description; Make description more specific |
| YAML parse error | Check indentation (spaces not tabs); Verify `---` placement |
| Name không match folder | Rename folder hoặc update `name` field |
| Description quá vague | Add file types, operations, "Use when..." clause |
| Multiple skills conflict | Make descriptions distinct; Use different trigger words |
| Scripts không chạy | Check execute permissions; Verify shebang; Test locally |
| Links broken | Use relative paths; Verify file exists |
| Too broad scope | Split into multiple focused skills |

---

## Validation Tools

### Manual Validation
```bash
# Check YAML syntax
head -n 20 SKILL.md

# Verify file structure
ls -la

# Test script permissions
ls -l scripts/

# Check line count
wc -l reference/*.md
```

### Automated Testing
```bash
# Restart AI AGENT
# Ask: "Can you [trigger phrase from description]?"
# Verify skill activates automatically
```

---

## Quick Validation Command

Sử dụng checklist này từng bước:

1. ✅ File structure → Section 1
2. ✅ YAML frontmatter → Section 2
3. ✅ Content quality → Section 3
4. ✅ Formatting → Section 4
5. ✅ Functionality → Section 5
6. ✅ Security → Section 6
7. ✅ Maintenance → Section 7
8. ✅ Final check → Section 8

Nếu tất cả checks pass → Skill ready!
