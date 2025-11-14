---
name: api-contracts-generator
description: Tạo API Contract đồng bộ Frontend (Next.js) ↔ Backend (NestJS) với types, validation, error handling. Dùng khi tạo APIs, DTOs, types frontend/backend hoặc khi người dùng nói đến "API", "DTO", "types", "contract", "validation", "synchronization"
allowed-tools: [Read, Write, Edit, Glob, Grep, Bash]
---

# API Contracts Generator

## Level 1: Overview

### Bài Toán

Trong một dự án full-stack, Frontend và Backend thường không đồng bộ:
- ❌ Types không khớp (`clubId` vs `id`)
- ❌ Validation khác nhau (backend max 100, frontend max 50)
- ❌ Error format không thống nhất
- ❌ Swagger không cập nhật

### Giải Pháp: API Contracts

**API Contract** định nghĩa giao diện Frontend ↔ Backend:

**Backend (NestJS)**:
- ✅ Request DTOs + validation (class-validator)
- ✅ Response DTOs + Swagger decorators
- ✅ Error handling chuẩn

**Frontend (Next.js)**:
- ✅ Types đồng bộ với backend
- ✅ Validation Zod giống backend
- ✅ Server Actions type-safe

### Output

```
✅ Types đồng bộ Frontend ↔ Backend
✅ Validation rules thống nhất
✅ Error format chuẩn
✅ Swagger tự động cập nhật
✅ 0 bugs from type mismatch
```

---

## Level 2: Quick Start

### Quy Trình 4 Bước

#### 1️⃣ Backend: Tạo DTOs (NestJS)

```typescript
// create-club.dto.ts
export class CreateClubDto {
  @IsString()
  @MinLength(3)
  @MaxLength(100)
  readonly name: string;

  @IsOptional()
  @MaxLength(500)
  readonly description?: string;
}

// club-detail.dto.ts
export class ClubDetailDto {
  @ApiProperty()
  id: string;
  
  @ApiProperty()
  name: string;
  
  @ApiProperty()
  owner: OwnerDto;
}
```

**Quy tắc**:
- Luôn có `@ApiProperty` cho Response DTOs
- Luôn có validation decorators cho Request DTOs
- `readonly` để immutable
- Ví dụ trong Swagger (`example`)

#### 2️⃣ Frontend: Tạo Types & Validation (Next.js)

**Cách 1: Sinh từ Swagger** (Khuyến cáo)
```bash
npx openapi-typescript http://localhost:3000/api-json -o src/types/api.ts
```

**Cách 2: Viết Zod Schema** (nếu không dùng OpenAPI)
```typescript
// club.schema.ts
export const createClubSchema = z.object({
  name: z.string().min(3).max(100),
  description: z.string().max(500).optional(),
});
```

⚠️ **CRITICAL**: Validation Zod phải CHÍNH XÁC khớp backend

#### 3️⃣ Frontend: Server Action (Orchestration)

```typescript
'use server';

export async function createClubAction(input: CreateClubInput) {
  try {
    // 1. Validate locally
    const validated = createClubSchema.parse(input);
    
    // 2. Call backend
    const response = await fetch('/api/clubs', {
      method: 'POST',
      body: JSON.stringify(validated),
    });
    
    // 3. Revalidate cache
    revalidatePath('/clubs');
    
    return { success: true, data: response };
  } catch (error) {
    return { success: false, error: { code: 'ERROR', message: '...' } };
  }
}
```

#### 4️⃣ Backend: Controller Endpoint

```typescript
@Post()
@ApiResponse({ status: 201, type: ClubDetailDto })
async create(@Body() dto: CreateClubDto): Promise<ClubDetailDto> {
  // Auto validated by class-validator + NestJS
  // Swagger auto documented by @ApiProperty
  return this.clubService.create(dto);
}
```

### Chiến Lược Đồng Bộ Types

| Chiến lược | Ưu điểm | Nhược điểm |
|-----------|---------|-----------|
| **OpenAPI Codegen** | Auto, luôn cập nhật | Phức tạp setup |
| **Shared Types (Monorepo)** | Tập trung | Cần monorepo |
| **Manual Duplicate** | Đơn giản | Dễ bị mismatch |

**Khuyến cáo**: OpenAPI + Codegen hoặc Monorepo

---

## Level 3: References

Chi tiết đầy đủ:
- [workflow.md](./references/workflow.md) - Backend DTOs, Frontend types, Server Actions, Error handling
- [validation-rules.md](./references/validation-rules.md) - Lỗi phổ biến, checklist đồng bộ

### Kiểm Tra Đồng Bộ

```bash
# 1. Backend: Swagger running?
curl http://localhost:3000/api

# 2. Frontend: Types generated?
ls src/types/api.ts

# 3. Match validation rules
# Backend: @MaxLength(100)
# Frontend: .max(100) ✅

# 4. Error format chuẩn?
# { code: "...", message: "...", details?: {...} }
```

### Errors Common

| Lỗi | Nguyên nhân | Fix |
|-----|-----------|-----|
| Type mismatch | Fields khác nhau | Sinh lại từ Swagger |
| Validation fail | Rules khác nhau | Kiểm tra min/max/required |
| Swagger không update | DTOs thay đổi | Restart backend server |
| Server Action error | API call fail | Kiểm tra error response format |

---

## ✅ Checklist Hoàn Thành

- [ ] Backend DTOs created + Swagger decorators
- [ ] Frontend types generated hoặc shared
- [ ] Zod validation khớp backend rules
- [ ] Server Actions type-safe + error handling
- [ ] Error response format thống nhất
- [ ] Swagger working at /api
- [ ] Test: Frontend call → Backend response ✅
