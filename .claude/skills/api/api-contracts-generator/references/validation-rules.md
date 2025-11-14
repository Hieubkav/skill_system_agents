# Validation Rules - Đồng Bộ Frontend ↔ Backend

## Các Lỗi Phổ Biến

### 1. ❌ Types Không Khớp
```typescript
// ❌ WRONG
// Backend gửi: { clubId: "123" }
// Frontend đợi: { id: "123" }

// ✅ RIGHT
// Backend: { id: "123" }
// Frontend: { id: "123" }
```

### 2. ❌ Validation Rules Khác Nhau
```typescript
// Backend (class-validator)
@MaxLength(100)

// ❌ Frontend (Zod) - SAI
.max(50)

// ✅ Frontend (Zod) - ĐÚNG
.max(100)
```

### 3. ❌ Error Format Không Thống Nhất
```typescript
// ❌ WRONG - Nhiều format khác nhau
POST /api/clubs → { error: "Invalid" }
POST /api/users → { message: "Validation failed", errors: [...] }

// ✅ RIGHT - Cùng format
{ code: "VALIDATION_ERROR", message: "...", details?: {...} }
```

### 4. ❌ Swagger Không Cập Nhật
- ✅ Tự động từ DTOs + decorators
- ❌ Viết tay rồi quên cập nhật

### 5. ❌ Server Actions Có Business Logic
- ✅ Server Actions = mỏng (call API + cache revalidate)
- ❌ Không đặt business logic ở Server Actions

## Checklist Đồng Bộ

### Backend (NestJS)
- [ ] Request DTOs với class-validator
- [ ] Response DTOs với @ApiProperty
- [ ] Validation decorators rõ ràng
- [ ] Error filter thống nhất
- [ ] Swagger accessible (/api)

### Frontend (Next.js)
- [ ] Types từ Swagger hoặc shared
- [ ] Zod schema khớp backend validation
- [ ] Server Actions type-safe
- [ ] Error handling chuẩn ApiError
- [ ] Error messages dịch cho user

### Đồng Bộ
- [ ] Validation rules giống nhau
- [ ] Field names giống nhau
- [ ] Error codes thống nhất
- [ ] Enum values đồng bộ
- [ ] Nullable/optional fields giống nhau
