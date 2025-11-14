# Duplicate Skill Detection

Hướng dẫn phát hiện và xử lý duplicate skills để tránh spam skills không hiệu quả.

## Table of Contents

- [Overview](#overview)
- [Khi Nào Check Duplicate](#khi-nào-check-duplicate)
- [Logic Detection](#logic-detection)
- [Similarity Scoring](#similarity-scoring)
- [Action Recommendations](#action-recommendations)
- [Implementation Steps](#implementation-steps)
- [Examples](#examples)

---

## Overview

Khi tạo skill mới, cần check xem đã có skill tương tự chưa để:
- **Tránh duplicate** - Không tạo skill trùng lặp
- **Tối ưu hóa** - Gộp/cải tiến skills hiện có thay vì tạo mới
- **Dễ quản lý** - Giữ skill system gọn gàng, không spam
- **Hiệu quả hơn** - Một skill tốt > nhiều skills yếu

**Nguyên tắc**: Better to improve existing skill than create new duplicate one.

---

## Khi Nào Check Duplicate

### Timing

Check duplicate **NGAY SAU STEP 1** (Xác định phạm vi Skill) trong workflow:

```
Step 1: Xác định phạm vi Skill
  ↓
Step 1.5: Check Duplicate Skills (NEW!)
  ↓
Step 2: Chọn Skill location (nếu không duplicate)
```

### Input Needed

Để check duplicate, cần:
1. **Skill name** (proposed name từ user)
2. **Skill description** (purpose và scope)
3. **Key operations** (create, analyze, extract, etc.)
4. **Domain/category** (API, database, UI, git, etc.)

### Process

1. User đề xuất skill mới với name/description
2. AI AGENT reads `active_skill.md` để lấy danh sách skills hiện có
3. Compare với từng skill hiện có
4. Calculate similarity score
5. Nếu detect high/medium similarity → show warning với recommendations
6. User chọn action: merge/delete/refine/keep-both/cancel

---

## Logic Detection

### Comparison Criteria

Check 4 tiêu chí chính:

#### 1. Name Similarity (30% weight)

**Exact Match**:
- `pdf-processor` vs `pdf-processor` → 100% match
- **Action**: Likely duplicate, investigate immediately

**Partial Match**:
- `pdf-processor` vs `pdf-handler` → High similarity
- `json-merger` vs `json-processor` → Medium similarity
- **Action**: Check description để confirm

**Algorithms**:
- Levenshtein distance
- Common substring
- Keyword overlap (split by hyphens)

#### 2. Description Keyword Overlap (40% weight)

**Extract Keywords**:
- Remove stopwords (the, and, or, a, an, when, use, etc.)
- Extract nouns, verbs, domain terms
- Include file extensions (.pdf, .json, .xlsx)

**Example**:
```
Skill A: "Extract text and tables from PDF files, fill forms"
Keywords: [extract, text, tables, PDF, files, fill, forms]

Skill B: "Process PDF documents, extract data from forms"
Keywords: [process, PDF, documents, extract, data, forms]

Common: [PDF, extract, forms] → 3/7 = 43% overlap
```

**Scoring**:
- >60% overlap → High similarity
- 30-60% overlap → Medium similarity
- <30% overlap → Low similarity

#### 3. Trigger Words Overlap (20% weight)

**Compare Triggers**:
```
Skill A triggers: ["PDF", "extract PDF", "PDF forms", "fill PDF"]
Skill B triggers: ["PDF files", "process PDF", "PDF data"]

Common: ["PDF"] → Some overlap
```

**Scoring**:
- >50% overlapping triggers → High similarity
- 25-50% overlap → Medium similarity
- <25% overlap → Low similarity

#### 4. Domain/Category Match (10% weight)

**Same Group Skill**:
- Both in `documents/` → Same domain
- Both in `api/` → Same domain

**Same Operations**:
- Both do "extract" → Similar operation
- Both do "analyze" → Similar operation

**Scoring**:
- Same group + similar operations → High similarity
- Same group only → Medium similarity
- Different groups → Low similarity (but still possible overlap)

---

## Similarity Scoring

### Overall Score Formula

```
Total Score = (Name × 0.3) + (Description × 0.4) + (Triggers × 0.2) + (Domain × 0.1)
```

### Score Ranges

| Score | Level | Action |
|-------|-------|--------|
| **≥70%** | **HIGH** | ⚠️ Likely duplicate - strongly warn user |
| **40-69%** | **MEDIUM** | ⚠️ Possible overlap - suggest review |
| **<40%** | **LOW** | ✅ OK to proceed - minimal overlap |

### Warning Messages

#### HIGH Similarity (≥70%)

```
⚠️ DUPLICATE DETECTED (Similarity: 85%)

Skill mới bạn đang tạo rất giống với skill hiện có:

📍 Skill hiện có: pdf-processor
   - Đường dẫn: \.claude\skills\documents\pdf-processor
   - Mô tả: Extract text and tables from PDF files, fill forms, merge documents
   
🔍 Độ giống:
   - Name: 90% (pdf-processor vs pdf-handler)
   - Description: 85% (7/9 keywords trùng)
   - Triggers: 80% (4/5 triggers trùng)
   - Domain: 100% (cùng group documents/)

💡 GỢI Ý ACTIONS:
1. [MERGE] Gộp vào skill hiện có (khuyến nghị)
2. [REFINE] Cải tiến skill cũ với features mới
3. [CANCEL] Hủy tạo skill mới
4. [KEEP-BOTH] Giữ cả 2 (phải clarify scopes khác nhau)

Bạn muốn làm gì? (nhập số 1-4)
```

#### MEDIUM Similarity (40-69%)

```
⚠️ POSSIBLE OVERLAP (Similarity: 55%)

Skill mới có thể overlap với:

📍 Skill hiện có: json-processor
   - Đường dẫn: \.claude\skills\data\json-processor
   - Mô tả: Process, merge, filter JSON files
   
🔍 Độ giống:
   - Name: 40% (data-merger vs json-processor)
   - Description: 50% (4/8 keywords trùng)
   - Triggers: 60% (3/5 triggers trùng)
   - Domain: 100% (cùng group data/)

💡 GỢI Ý:
- Review skill hiện có trước khi tạo mới
- Consider mở rộng skill cũ nếu features liên quan
- Nếu scopes khác nhau, clarify trong description

Tiếp tục tạo skill mới? (y/n)
```

---

## Action Recommendations

Sau khi detect duplicate, đưa ra 4-5 options cho user:

### Option 1: MERGE (Gộp skills)

**Khi nào dùng**:
- 2 skills có chức năng tương tự
- Có thể combine thành 1 skill mạnh hơn
- Không mất functionality quan trọng

**Steps**:
1. Combine descriptions → new comprehensive description
2. Merge trigger words → union of both trigger lists
3. Combine reference files (nếu cần)
4. Update SKILL.md với combined content
5. **Xóa registration skill cũ** trong active_skill.md
6. **Add registration skill merged mới** trong active_skill.md
7. Delete old skill folder (hoặc archive)

**Example**:
```
Skill A: pdf-extractor (extract text from PDF)
Skill B: pdf-form-filler (fill PDF forms)
  ↓
Merged: pdf-processor (extract text + fill forms)
```

### Option 2: DELETE OLD (Xóa skill cũ, giữ mới)

**Khi nào dùng**:
- Skill mới superior hơn skill cũ
- Skill cũ outdated hoặc poorly designed
- Skill mới cover tất cả features của skill cũ + more

**Steps**:
1. Review skill cũ để ensure không mất features quan trọng
2. **Xóa registration trong active_skill.md** (skill cũ)
3. Delete old skill folder
4. Proceed với tạo skill mới
5. **Add registration skill mới** trong active_skill.md
6. Notify team về deprecation (nếu team skill)

### Option 3: REFINE (Cải tiến skill cũ)

**Khi nào dùng**:
- Skill cũ good structure nhưng thiếu features
- Chỉ cần thêm functionality vào skill hiện có
- Không cần architectural changes lớn

**Steps**:
1. Open SKILL.md của skill cũ
2. Add new features vào description
3. Add new sections/steps trong Level 2/3
4. Add new trigger words
5. Update reference files (nếu cần)
6. **Update registration trong active_skill.md** (description và triggers)
7. Test updated skill

**Example**:
```
Old: api-designer (basic REST API design)
  ↓
Refined: api-designer (REST + GraphQL + OpenAPI specs)
```

### Option 4: KEEP BOTH (Giữ cả 2 skills)

**Khi nào dùng**:
- 2 skills có scopes khác nhau (sau khi clarify)
- Serve different use cases
- Operations level khác nhau (basic vs advanced)

**Steps**:
1. **Clarify scopes rõ ràng** cho cả 2 skills
2. Update descriptions để distinguish
3. Update trigger words để avoid conflicts
4. Proceed với tạo skill mới
5. **Add registration skill mới** trong active_skill.md
6. **Review registration skill cũ** để ensure no ambiguity

**Example**:
```
Skill A: api-designer (design API architecture, high-level)
Skill B: api-documentation-writer (write API docs, detailed)
  → Different scopes, keep both
```

### Option 5: CANCEL (Hủy tạo skill mới)

**Khi nào dùng**:
- Sau review, skill hiện có đã đủ tốt
- Không cần duplicate
- User quyết định không tạo nữa

**Steps**:
1. Cancel workflow
2. Không tạo skill mới
3. Có thể suggest improve skill hiện có nếu cần

---

## Implementation Steps

### Integration vào Workflow

**Update workflow.md** - Thêm Step 1.5:

```markdown
## Step 1.5: Check Duplicate Skills (NEW!)

### Automatic Detection

1. **Read active_skill.md**
   ```bash
   Read E:\Laravel\study\skill_system\.claude\hook\choose_skill.md\active_skill.md
   ```

2. **Extract all existing skills**
   - Parse skill names
   - Parse descriptions
   - Parse trigger words
   - Parse groups/domains

3. **Compare với skill mới**
   - Name similarity
   - Description keyword overlap
   - Trigger words overlap
   - Domain match

4. **Calculate similarity score**
   - Total score từ 4 tiêu chí
   - Classify: HIGH (≥70%), MEDIUM (40-69%), LOW (<40%)

### Actions Based on Score

**If HIGH similarity (≥70%)**:
- ⚠️ Show strong warning
- Present duplicate skill details
- Offer 4 options: MERGE / DELETE / REFINE / CANCEL
- **Stop workflow** until user decides

**If MEDIUM similarity (40-69%)**:
- ⚠️ Show warning
- Present possibly overlapping skill
- Ask user confirm: continue or review?
- Proceed if user confirms

**If LOW similarity (<40%)**:
- ✅ No significant overlap
- Continue to Step 2 (Chọn Skill location)

### Example Flow

```
User: "Tôi muốn tạo skill extract data from PDFs"
  ↓
AI AGENT: [Check active_skill.md...]
  ↓
AI AGENT: "⚠️ DUPLICATE DETECTED (85%)
          Skill 'pdf-processor' đã có chức năng tương tự.
          
          Options:
          1. MERGE vào pdf-processor
          2. REFINE pdf-processor với features mới
          3. CANCEL
          4. KEEP BOTH (clarify scopes)
          
          Bạn chọn option nào?"
  ↓
User: "1"
  ↓
AI AGENT: [Merge workflow...]
  ↓
Result: pdf-processor updated, no new skill created
```

---

## Examples

### Example 1: HIGH Similarity - MERGE

**Scenario**:
```
User: "Tạo skill fill PDF forms"

Existing: pdf-processor (extract text from PDF)
New: pdf-form-filler (fill PDF forms)

Similarity: 75%
- Name: 70% (pdf-processor vs pdf-form-filler)
- Description: 80% (PDF, forms, process)
- Triggers: 75% (PDF triggers overlap)
- Domain: 100% (documents/)
```

**AI Response**:
```
⚠️ HIGH similarity với pdf-processor.

Gợi ý: MERGE thành pdf-processor với cả extract và fill forms.

Bạn đồng ý merge? (y/n)
```

**User**: `y`

**Actions**:
1. Update pdf-processor SKILL.md:
   - Add "fill forms" vào description
   - Add form-filling steps
   - Add new triggers: "fill PDF form", "PDF forms"
2. Update active_skill.md:
   - Update Mô tả của pdf-processor
   - Add new triggers
3. No new skill created

### Example 2: MEDIUM Similarity - REFINE

**Scenario**:
```
User: "Tạo skill GraphQL API design"

Existing: api-designer (REST API design)
New: graphql-designer (GraphQL API design)

Similarity: 55%
- Name: 40% (api-designer vs graphql-designer)
- Description: 60% (API, design, endpoints)
- Triggers: 50% (API design triggers)
- Domain: 100% (api/)
```

**AI Response**:
```
⚠️ MEDIUM similarity với api-designer.

Gợi ý: Mở rộng api-designer support cả REST và GraphQL?

Options:
1. REFINE api-designer (add GraphQL)
2. KEEP BOTH (REST vs GraphQL separate)

Bạn chọn? (1/2)
```

**User**: `1`

**Actions**:
1. Update api-designer SKILL.md:
   - Change description: "REST and GraphQL API design"
   - Add GraphQL section trong Level 2
   - Add reference/graphql-patterns.md
2. Update active_skill.md:
   - Update Mô tả
   - Add triggers: "GraphQL", "GraphQL API", "thiết kế GraphQL"
3. No new skill created

### Example 3: LOW Similarity - PROCEED

**Scenario**:
```
User: "Tạo skill git commit message generator"

Existing: api-designer (API design)
New: commit-helper (git commits)

Similarity: 20%
- Name: 10% (completely different)
- Description: 15% (no keyword overlap)
- Triggers: 0% (no overlap)
- Domain: 0% (api/ vs git/)
```

**AI Response**:
```
✅ No significant overlap detected.

Proceeding với skill creation...
```

**Actions**:
- Continue to Step 2 (Chọn Skill location)
- No warnings

---

## Best Practices

### 1. Always Check Before Creating

- **Mandatory check** cho mọi skill mới
- Không skip step này
- Better prevention than cleanup later

### 2. Be Honest About Similarity

- Không ignore warnings
- Review duplicates thoroughly
- Think: "Can I improve existing skill instead?"

### 3. Prefer Refinement Over Creation

- **Better 1 strong skill > 2 weak skills**
- Merge/refine khi possible
- Only create new nếu truly different scope

### 4. Clear Scope Distinction

- Nếu keep both, ensure scopes rõ ràng
- Update descriptions để distinguish
- Different trigger words để avoid conflicts

### 5. Keep active_skill.md Clean

- Remove deprecated skills promptly
- Update registrations khi merge/refine
- Document changes trong git commits

### 6. Regular Audits

- Định kỳ review skill list
- Identify và merge duplicates
- Keep skill system optimized

---

## Summary

**Key Points**:

1. ✅ Check duplicate **NGAY SAU Step 1** trong workflow
2. ✅ Score dựa trên: Name (30%) + Description (40%) + Triggers (20%) + Domain (10%)
3. ✅ HIGH (≥70%) → Strong warning, must decide
4. ✅ MEDIUM (40-69%) → Warning, suggest review
5. ✅ LOW (<40%) → Proceed normally
6. ✅ Options: MERGE / DELETE / REFINE / KEEP-BOTH / CANCEL
7. ✅ Always update active_skill.md sau khi action
8. ✅ Prefer improve existing > create new

**Philosophy**: **One good skill >> Many mediocre skills**

Keep skill system lean, efficient, và dễ quản lý! 🚀
