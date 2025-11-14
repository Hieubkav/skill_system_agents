# Advanced Topics

## Models to Observe

Apply cache invalidation cho TẤT CẢ models quan trọng:

```php
// Menu system
- Menu
- MenuBlock  
- MenuBlockItem

// Content
- HomeComponent
- Product
- Article
- Image

// Taxonomy
- CatalogTerm (optional)
- CatalogAttributeGroup (optional)
```

## Cache Strategy Comparison

| Strategy | Update Time | Server Load | Use Case |
|----------|------------|-------------|----------|
| **No cache** | Real-time | 🔥 Very high | Dev only |
| **Time-based only (10s)** | 10 seconds | ✅ Low | Simple sites |
| **On-demand only** | 1-2 seconds | ⚠️ Medium | Medium traffic |
| **Hybrid (10s + On-demand)** | 1-2s with fallback | ✅ Optimal | **RECOMMENDED** |

## Performance Considerations

### Optimal Settings
- **Revalidate time:** 10 seconds (balance between freshness và load)
- **HTTP timeout:** 5 seconds (avoid blocking)
- **Fail silently:** Log warning nhưng không crash

### Load Testing
```bash
# Simulate 100 requests
ab -n 100 -c 10 http://127.0.0.1:8000/api/v1/menus

# Check response time
curl -w "@curl-format.txt" -o /dev/null -s http://127.0.0.1:8000/api/v1/menus
```

## Key Principles

1. **Dual-layer protection:** Time-based (10s) + On-demand (instant)
2. **Fail gracefully:** On-demand fail → Time-based fallback
3. **Cache version:** Track changes, useful for debugging
4. **Observer pattern:** DRY, centralized cache logic
5. **Secure endpoint:** Always validate secret token
6. **Log everything:** Essential for debugging production issues

## Security Checklist

- [ ] ✅ Secret token đủ mạnh (min 32 chars)
- [ ] ✅ Secret khác nhau giữa dev và production
- [ ] ✅ Endpoint không expose trong public docs
- [ ] ✅ Timeout để prevent DoS
- [ ] ✅ Rate limiting (optional)

## Quick Commands

```bash
# Backend: Increment version manually
php artisan tinker --execute="Cache::increment('api_cache_version');"

# Backend: Trigger revalidation
php artisan tinker --execute="app(\App\Services\RevalidationService::class)->revalidateAll();"

# Frontend: Check endpoint health
curl http://localhost:3000/api/revalidate

# Frontend: Rebuild with new cache
rm -rf .next && npm run build
```

## Success Metrics

✅ **User không phàn nàn "phải Ctrl+F5"**  
✅ **Admin update → User thấy mới trong 1-2s**  
✅ **Server load không tăng đáng kể**  
✅ **Zero downtime khi deploy**  
✅ **API response time < 500ms**

Khi đạt được tất cả metrics trên → Hệ thống hoạt động tốt! 🎉
