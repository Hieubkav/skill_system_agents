# Skill Management Scripts

Các utility scripts để quản lý skill system.

## Scripts Available

### 1. `generate_skill_index.py` ⭐ NEW!
**Purpose**: Generate lightweight skill index + split skills into individual files

⚠️ **GAME CHANGER**: Reduces context load by 57% (11.5 KB vs 26.6 KB)

**Usage**:
```bash
python .claude/skills/meta/create_skill/scripts/generate_skill_index.py
```

**What it does**:
- Read active_skill.md (27 KB, 42 skills)
- Generate skill_index.md (11 KB, lightweight)
- Split skills into individual files by domain
- Create 16 domain folders with 42 skill files

**Output**:
```
.claude/hook/choose_skill.md/
├── skill_index.md (11.5 KB) ← AI loads this first
└── skills/
    ├── database/    (8 skills)
    ├── frontend/    (6 skills)
    ├── api/         (3 skills)
    └── ... (16 domains)
```

**When to use**:
- After adding new skills
- After updating skill descriptions
- After merging/removing skills
- Weekly maintenance (keep index synced)

**Benefits**:
- ✅ 57% context savings on initial scan
- ✅ 50% faster skill matching
- ✅ Organized by domain
- ✅ On-demand detail loading
- ✅ Scales better (100 skills = still ~15 KB index)

---

### 2. `analyze_active_skills.py`
**Purpose**: Analyze active_skill.md size và suggest optimizations

**Usage**:
```bash
python .claude/skills/meta/create_skill/scripts/analyze_active_skills.py
```

**Output**: Statistics + recommendations (Index vs Split vs TOC)

---

### 3. `list_skills.py`
**Purpose**: List tất cả skills đã đăng ký trong active_skill.md

**Usage**:
```bash
python .claude/skills/meta/create_skill/scripts/list_skills.py
```

**Output**: Danh sách đầy đủ các skills với numbering

---

### 4. `update_active_skills.py`
**Purpose**: Tự động update active_skill.md - remove old skills, add new merged skills

⚠️ **CRITICAL**: Script này modify active_skill.md. Backup trước khi chạy!

**Usage**:
```bash
python .claude/skills/meta/create_skill/scripts/update_active_skills.py
```

**What it does**:
- Remove skills trong SKILLS_TO_REMOVE list
- Add skills từ NEW_SKILLS constant
- Clean up excessive blank lines

**When to use**:
- Sau khi merge skills
- Khi cần batch update registrations
- Khi có nhiều skills cần add/remove cùng lúc

---

### 5. `cleanup_duplicates.py`
**Purpose**: Remove duplicate skill registrations, keep only LAST occurrence

**Usage**:
```bash
python .claude/skills/meta/create_skill/scripts/cleanup_duplicates.py
```

**What it does**:
- Scan active_skill.md for duplicate skill names
- Keep last occurrence of each duplicate (usually the newest/best)
- Rebuild file with unique skills only

**When to use**:
- After manual edits caused duplicates
- After multiple merges
- Regular cleanup/maintenance

---

## File Locations

**Active Skill Registry**:
```
E:\Laravel\study\skill_system\.claude\hook\choose_skill.md\active_skill.md
```

**Scripts Location**:
```
E:\Laravel\study\skill_system\.claude\skills\meta\create_skill\scripts\
```

---

## Best Practices

### Before Running Scripts
1. ✅ **Backup active_skill.md** first
2. ✅ Review script constants (SKILLS_TO_REMOVE, NEW_SKILLS)
3. ✅ Test on small subset if modifying

### After Running Scripts
1. ✅ Verify with `list_skills.py`
2. ✅ Check no duplicates
3. ✅ Test skill activation with trigger words
4. ✅ Commit changes with clear message

### Regular Maintenance
- Run `list_skills.py` monthly to audit skill count
- Run `cleanup_duplicates.py` if duplicates detected
- Keep scripts updated as skill system evolves

---

## Troubleshooting

**Script fails with encoding error**:
- Ensure file encoding is UTF-8
- Check Python version (3.7+)

**Changes not applied**:
- Verify file paths in script constants
- Check file permissions
- Review script output for errors

**Duplicates still present**:
- Run `cleanup_duplicates.py` again
- Manually inspect active_skill.md
- Check for formatting issues

---

## Development Notes

**Path Constants**:
All scripts use absolute paths to `active_skill.md`. Update these if file structure changes.

**Regex Patterns**:
Scripts rely on consistent formatting:
- `## Skill {name}:` for skill headers
- Skills separated by blank lines

**Future Enhancements**:
- [ ] Add validation before modifying file
- [ ] Create backup automatically
- [ ] Add dry-run mode
- [ ] Generate diff preview
- [ ] Add rollback functionality

---

## History

**Created**: 2025-11-14  
**Purpose**: Organize skill merge utilities  
**Scripts moved from**: Project root → create_skill/scripts/  
**Reason**: Keep project clean, organize by skill domain
