---
name: api-cache-invalidation
description: Automatic cache invalidation system với Laravel Observers và Next.js On-Demand Revalidation. Tự động sync data real-time giữa backend và frontend khi admin update. USE WHEN cần setup cache management, sync frontend-backend, API cache strategy, hoặc user phàn nàn "phải Ctrl+F5 mới thấy data mới".
---

# API Cache Invalidation

## Level 1: Overview

### Skill này làm gì?

Tự động vô hiệu hóa cache khi data thay đổi, giúp frontend luôn hiển thị data mới nhất mà không cần Ctrl+F5.

### Khi nào dùng skill này?

- User nói "cache không update"
- User nói "phải Ctrl+F5 mới thấy data mới"
- User muốn "sync data real-time"
- Cần setup cache strategy cho API
- Frontend không reflect backend changes
- User mentions "cache invalidation" or "revalidation"

### Core Components

**1. Backend: Laravel Observers + Cache Version**
- Observer detect model changes (create/update/delete)
- Auto-increment cache version
- Trigger Next.js on-demand revalidation
- Return cache version trong API response

**2. Frontend: Next.js ISR + On-Demand Revalidation**
- Time-based: Revalidate mỗi 10s (fallback)
- On-demand: Instant revalidation khi backend trigger
- Cache version tracking
- Revalidation API endpoint

### Yêu cầu

- Laravel backend với Observers
- Next.js frontend với App Router
- Shared secret key giữa backend và frontend
- HTTP client (Guzzle) cho backend

---

## Level 2: Quick Start

### Testing the System

### Test 1: Check Cache Version
```bash
curl http://127.0.0.1:8000/api/v1/menus | jq '.meta.cache_version'
# Output: 4
```

### Test 2: Update Data
```bash
# Update menu trong admin panel hoặc:
php artisan tinker
> $menu = App\Models\Menu::first();
> $menu->touch();
```

### Test 3: Verify Version Increment
```bash
curl http://127.0.0.1:8000/api/v1/menus | jq '.meta.cache_version'
# Output: 5 (đã tăng!)
```

### Test 4: Check Frontend Update
```bash
# F5 trình duyệt trong 1-2 giây → Thấy data mới!
```

### Common Issues & Solutions

**Issue 1: "Revalidation not working"**
- Next.js server đang chạy?
- NEXT_REVALIDATE_URL đúng?
- NEXT_REVALIDATE_SECRET khớp giữa backend và frontend?

**Debug:**
```bash
# Check logs
tail -f storage/logs/laravel.log | grep "revalidation"

# Test endpoint
curl -X POST http://localhost:3000/api/revalidate \
  -H "Content-Type: application/json" \
  -d '{"secret":"your-secret","paths":["/"]}'
```

**Issue 2: "Cache version not incrementing"**
- Observer đã được register? (Model có #[ObservedBy] attribute?)
- incrementCacheVersion() được gọi trong created/updated/deleted?

**Debug:**
```bash
php artisan tinker
> Cache::get('api_cache_version')
> Cache::put('api_cache_version', 0) # Reset for testing
```

**Issue 3: "Frontend still shows old data"**
- Browser cache? (Hard refresh: Ctrl+Shift+R)
- Next.js build cache? (Delete .next folder và rebuild)
- API response có meta.cache_version?

---

## Level 3: References

### Chi tiết

- **Advanced Topics**: `read .claude/skills/api/api-cache-invalidation/references/advanced-topics.md`
  - Models to observe
  - Cache strategy comparison
  - Performance considerations
  - Key principles
  - Security checklist
  - Quick commands
  - Success metrics

- **Comprehensive Guide**: `read .claude/skills/api/api-cache-invalidation/references/comprehensive-guide.md`
  - Step-by-step implementation
  - Complete code examples

- **Quick Setup Workflow**: `read .claude/skills/api/api-cache-invalidation/references/quick-setup-workflow.md`
  - Fast implementation checklist

### Related Skills

- `read .claude/skills/api/api-design-principles/SKILL.md` (if exists)
- `read .claude/skills/filament/filament-rules/SKILL.md` (if exists)
