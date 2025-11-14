# API Contracts - Quy Trình Chi Tiết

## 📦 Backend DTOs (NestJS)

### Request DTOs (Input)

```typescript
// volley-app-backend/src/club-management/presentation/dtos/create-club.dto.ts

import { IsString, IsNotEmpty, IsOptional, MaxLength, MinLength } from 'class-validator';
import { ApiProperty, ApiPropertyOptional } from '@nestjs/swagger';

export class CreateClubDto {
  @ApiProperty({
    description: 'Club name',
    example: 'Volley Club Paris',
    minLength: 3,
    maxLength: 100,
  })
  @IsString()
  @IsNotEmpty()
  @MinLength(3)
  @MaxLength(100)
  readonly name: string;

  @ApiPropertyOptional({
    description: 'Club description',
    example: 'Best volleyball club in Paris',
    maxLength: 500,
  })
  @IsString()
  @IsOptional()
  @MaxLength(500)
  readonly description?: string;
}
```

**Quy tắc Request DTOs**:
- ✅ Validation với `class-validator` (IsString, IsNotEmpty, etc.)
- ✅ Swagger decorators `@ApiProperty` cho documentation
- ✅ `readonly` cho immutability
- ✅ Ví dụ trong Swagger (`example`)
- ❌ Không có logika - chỉ validation

### Response DTOs (Output)

```typescript
// volley-app-backend/src/club-management/presentation/dtos/club-detail.dto.ts

import { ApiProperty, ApiPropertyOptional } from '@nestjs/swagger';

export class OwnerDto {
  @ApiProperty({ example: 'user-123' })
  id: string;

  @ApiProperty({ example: 'John Doe' })
  name: string;

  @ApiProperty({ example: 'john@example.com' })
  email: string;
}

export class SubscriptionDto {
  @ApiProperty({ example: 'FREE', enum: ['FREE', 'PRO', 'UNLIMITED'] })
  plan: string;

  @ApiProperty({ example: 'ACTIVE', enum: ['ACTIVE', 'INACTIVE', 'EXPIRED'] })
  status: string;

  @ApiProperty({ example: 1 })
  maxTeams: number;

  @ApiProperty({ example: 0 })
  currentTeamsCount: number;
}

export class ClubDetailDto {
  @ApiProperty({ example: 'club-123' })
  id: string;

  @ApiProperty({ example: 'Volley Club Paris' })
  name: string;

  @ApiPropertyOptional({ example: 'Best club in Paris' })
  description?: string;

  @ApiProperty({ type: OwnerDto })
  owner: OwnerDto;

  @ApiProperty({ type: SubscriptionDto })
  subscription: SubscriptionDto;

  @ApiProperty({ example: 15 })
  membersCount: number;

  @ApiProperty({ example: '2024-01-01T00:00:00.000Z' })
  createdAt: Date;
}
```

**Quy tắc Response DTOs**:
- ✅ Swagger decorators cho documentation đầy đủ
- ✅ Nested DTOs cho relations (OwnerDto, SubscriptionDto)
- ✅ Ví dụ thực tế
- ✅ Enum values documented
- ❌ Không dùng entités domain brutes

### Pagination DTO

```typescript
// volley-app-backend/src/shared/dtos/pagination.dto.ts

import { ApiProperty, ApiPropertyOptional } from '@nestjs/swagger';
import { Type } from 'class-transformer';
import { IsInt, IsOptional, Max, Min } from 'class-validator';

export class PaginationQueryDto {
  @ApiPropertyOptional({ default: 1, minimum: 1 })
  @IsOptional()
  @Type(() => Number)
  @IsInt()
  @Min(1)
  page?: number = 1;

  @ApiPropertyOptional({ default: 10, minimum: 1, maximum: 100 })
  @IsOptional()
  @Type(() => Number)
  @IsInt()
  @Min(1)
  @Max(100)
  limit?: number = 10;
}

export class PaginationMetaDto {
  @ApiProperty({ example: 1 })
  page: number;

  @ApiProperty({ example: 10 })
  limit: number;

  @ApiProperty({ example: 50 })
  total: number;

  @ApiProperty({ example: 5 })
  totalPages: number;
}

export class PaginatedResponseDto<T> {
  @ApiProperty({ isArray: true })
  data: T[];

  @ApiProperty({ type: PaginationMetaDto })
  meta: PaginationMetaDto;
}
```

### Controller Integration

```typescript
// volley-app-backend/src/club-management/presentation/controllers/clubs.controller.ts

import { Controller, Post, Get, Body, Param, Query, UseGuards } from '@nestjs/common';
import { ApiTags, ApiOperation, ApiResponse, ApiBearerAuth } from '@nestjs/swagger';
import { JwtAuthGuard } from '../../auth/guards/jwt-auth.guard';
import { CreateClubDto } from '../dtos/create-club.dto';
import { ClubDetailDto } from '../dtos/club-detail.dto';

@ApiTags('Clubs')
@ApiBearerAuth()
@Controller('clubs')
@UseGuards(JwtAuthGuard)
export class ClubsController {
  @Post()
  @ApiOperation({ summary: 'Create a new club' })
  @ApiResponse({ status: 201, description: 'Club created', type: String })
  async create(@Body() dto: CreateClubDto): Promise<{ id: string }> {
    // Implementation
  }

  @Get(':id')
  @ApiOperation({ summary: 'Get club details' })
  @ApiResponse({ status: 200, description: 'Club found', type: ClubDetailDto })
  async findOne(@Param('id') id: string): Promise<ClubDetailDto> {
    // Implementation
  }
}
```

## 🎨 Frontend Types (Next.js)

### Chiến lược đồng bộ

**Option 1: Tạo từ Swagger** (Khuyến cáo)
```bash
npm install --save-dev openapi-typescript
npx openapi-typescript http://localhost:3000/api-json -o src/types/api.ts
```

**Option 2: Chia sẻ types (Monorepo)**
```typescript
// shared/types/club.types.ts
export interface CreateClubInput {
  name: string;
  description?: string;
}

export interface ClubDetail {
  id: string;
  name: string;
  description?: string;
  owner: { id: string; name: string; email: string };
  subscription: { plan: string; status: string; maxTeams: number };
  membersCount: number;
  createdAt: Date;
}
```

### Validation Frontend (Zod)

```typescript
// volley-app-frontend/src/features/club-management/schemas/club.schema.ts

import { z } from 'zod';

export const createClubSchema = z.object({
  name: z
    .string()
    .min(3, 'Tên phải ≥ 3 ký tự')
    .max(100, 'Tên phải ≤ 100 ký tự'),
  description: z
    .string()
    .max(500, 'Mô tả phải ≤ 500 ký tự')
    .optional(),
});

export type CreateClubInput = z.infer<typeof createClubSchema>;
```

⚠️ **CRITICAL**: Quy tắc Zod phải CHÍNH XÁC khớp với backend (class-validator)

## 🔗 Server Actions (Frontend → Backend)

```typescript
// volley-app-frontend/src/features/club-management/actions/create-club.action.ts
'use server';

import { revalidatePath } from 'next/cache';
import { createClubSchema, CreateClubInput } from '../schemas/club.schema';
import { clubsApi } from '../api/clubs.api';

export async function createClubAction(input: CreateClubInput) {
  try {
    const validated = createClubSchema.parse(input);
    const response = await clubsApi.create(validated);
    revalidatePath('/dashboard/coach');
    return { success: true as const, data: response };
  } catch (error) {
    return {
      success: false as const,
      error: {
        code: error instanceof Error ? 'UNKNOWN_ERROR' : 'VALIDATION_ERROR',
        message: error.message || 'Lỗi',
      },
    };
  }
}
```

## ⚠️ Error Handling

### Backend Format

```typescript
// volley-app-backend/src/shared/filters/http-exception.filter.ts

export interface ErrorResponse {
  code: string;
  message: string;
  details?: any;
  timestamp: string;
  path: string;
}
```

### Frontend Handler

```typescript
export class ApiError extends Error {
  constructor(
    public code: string,
    message: string,
    public details?: any,
  ) {
    super(message);
    this.name = 'ApiError';
  }

  getUserMessage(): string {
    const messages: Record<string, string> = {
      VALIDATION_ERROR: 'Dữ liệu không hợp lệ',
      NOT_FOUND: 'Không tìm thấy tài nguyên',
      UNAUTHORIZED: 'Cần đăng nhập',
      FORBIDDEN: 'Không có quyền',
      INTERNAL_SERVER_ERROR: 'Lỗi server. Thử lại.',
    };
    return messages[this.code] || this.message;
  }
}
```
