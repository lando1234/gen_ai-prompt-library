---
type: code-generation
complexity: basic
context: technical
output_format: code
interaction_mode: single-shot
production_ready: experimental
tags: [crud, api, database, orm, backend]
---

# Generate CRUD Operations for Entity

## Purpose
Generate complete Create, Read, Update, Delete operations for a database entity with validation, error handling, and API endpoints.

## Context
Use this prompt when:
- Scaffolding new database-backed resources
- Building standard REST APIs quickly
- Need consistent CRUD patterns across entities
- Starting a new backend service

---

## Prompt

You are a backend developer. Generate complete CRUD operations for a database entity with API endpoints, validation, and error handling.

**INPUT**:
- Entity name and fields (with types, constraints)
- Target stack (e.g., Node.js + PostgreSQL, Python + MongoDB)
- Optional: Authentication requirements

**GENERATE**:

1. **Database Model**:
   - ORM/ODM model definition
   - Field types, constraints (required, unique, min/max)
   - Indexes for performance
   - Timestamps (created_at, updated_at)

2. **CRUD Functions**:
   - **Create**: Insert with validation, return created entity
   - **Read (Get by ID)**: Fetch single entity, 404 if not found
   - **Read (List/Filter)**: Pagination, filtering, sorting
   - **Update**: Partial or full update, merge with existing
   - **Delete**: Soft or hard delete, cascade if needed

3. **API Endpoints**:
   - POST /entities - Create
   - GET /entities/:id - Get by ID
   - GET /entities - List with filters
   - PUT/PATCH /entities/:id - Update
   - DELETE /entities/:id - Delete

4. **Validation**:
   - Required field checks
   - Type validation
   - Business rule validation (e.g., email format, positive numbers)

5. **Error Handling**:
   - 400: Invalid input
   - 404: Entity not found
   - 409: Unique constraint violation
   - 500: Database errors

**OUTPUT FORMAT**:
```
# CRUD Operations: [Entity Name]

## 1. Database Model
```[language]
[model definition]
```

## 2. CRUD Functions

### Create
```[language]
[create function]
```

### Read (Get by ID)
```[language]
[get by id function]
```

### List (with filters)
```[language]
[list function with pagination/filtering]
```

### Update
```[language]
[update function]
```

### Delete
```[language]
[delete function]
```

## 3. API Routes
```[language]
[route definitions]
```

## 4. Request/Response Examples

[Examples for each operation]

## 5. Notes
- [Implementation decisions]
- [Security considerations]
- [Performance tips]
```

**CONSTRAINTS**:
- Include validation for all inputs
- Handle errors gracefully with appropriate HTTP codes
- Support pagination for list operations (limit/offset or cursor)
- Include filtering and sorting in list endpoint
- Use transactions where appropriate
- Follow RESTful conventions

---

## Example

**Input**:
```
Entity: Product
Fields:
  - name: string (required, max 200 chars, unique)
  - description: text (optional)
  - price: decimal (required, positive)
  - stock: integer (required, non-negative, default 0)
  - category: string (required)

Stack: Node.js + Express + PostgreSQL + Prisma
```

**Output**:

```typescript
# CRUD Operations: Product

## 1. Database Model

```typescript
// prisma/schema.prisma
model Product {
  id          Int      @id @default(autoincrement())
  name        String   @unique @db.VarChar(200)
  description String?  @db.Text
  price       Decimal  @db.Decimal(10, 2)
  stock       Int      @default(0)
  category    String   @db.VarChar(100)
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt

  @@index([category])
  @@index([createdAt])
}
```

## 2. CRUD Functions

### Create Product
```typescript
import { PrismaClient } from '@prisma/client';
import { z } from 'zod';

const prisma = new PrismaClient();

const productCreateSchema = z.object({
  name: z.string().min(1).max(200),
  description: z.string().optional(),
  price: z.number().positive(),
  stock: z.number().int().nonnegative().default(0),
  category: z.string().min(1).max(100)
});

export async function createProduct(data: unknown) {
  // Validate input
  const validated = productCreateSchema.parse(data);

  try {
    const product = await prisma.product.create({
      data: validated
    });
    return { success: true, data: product };
  } catch (error: any) {
    if (error.code === 'P2002') {
      throw new Error(`Product with name "${validated.name}" already exists`);
    }
    throw error;
  }
}
```

### Get Product by ID
```typescript
export async function getProductById(id: number) {
  const product = await prisma.product.findUnique({
    where: { id }
  });

  if (!product) {
    throw new Error(`Product with ID ${id} not found`);
  }

  return { success: true, data: product };
}
```

### List Products (with filtering)
```typescript
const productListSchema = z.object({
  category: z.string().optional(),
  minPrice: z.number().positive().optional(),
  maxPrice: z.number().positive().optional(),
  limit: z.number().int().positive().max(100).default(20),
  offset: z.number().int().nonnegative().default(0),
  sortBy: z.enum(['name', 'price', 'stock', 'createdAt']).default('createdAt'),
  sortOrder: z.enum(['asc', 'desc']).default('desc')
});

export async function listProducts(filters: unknown) {
  const validated = productListSchema.parse(filters);

  const where: any = {};
  if (validated.category) where.category = validated.category;
  if (validated.minPrice || validated.maxPrice) {
    where.price = {};
    if (validated.minPrice) where.price.gte = validated.minPrice;
    if (validated.maxPrice) where.price.lte = validated.maxPrice;
  }

  const [products, total] = await Promise.all([
    prisma.product.findMany({
      where,
      take: validated.limit,
      skip: validated.offset,
      orderBy: { [validated.sortBy]: validated.sortOrder }
    }),
    prisma.product.count({ where })
  ]);

  return {
    success: true,
    data: products,
    pagination: {
      total,
      limit: validated.limit,
      offset: validated.offset,
      hasMore: validated.offset + products.length < total
    }
  };
}
```

### Update Product
```typescript
const productUpdateSchema = z.object({
  name: z.string().min(1).max(200).optional(),
  description: z.string().optional().nullable(),
  price: z.number().positive().optional(),
  stock: z.number().int().nonnegative().optional(),
  category: z.string().min(1).max(100).optional()
}).refine(data => Object.keys(data).length > 0, {
  message: 'At least one field must be provided for update'
});

export async function updateProduct(id: number, data: unknown) {
  const validated = productUpdateSchema.parse(data);

  try {
    const product = await prisma.product.update({
      where: { id },
      data: validated
    });
    return { success: true, data: product };
  } catch (error: any) {
    if (error.code === 'P2025') {
      throw new Error(`Product with ID ${id} not found`);
    }
    if (error.code === 'P2002') {
      throw new Error(`Product with name "${validated.name}" already exists`);
    }
    throw error;
  }
}
```

### Delete Product
```typescript
export async function deleteProduct(id: number) {
  try {
    await prisma.product.delete({
      where: { id }
    });
    return { success: true, message: 'Product deleted successfully' };
  } catch (error: any) {
    if (error.code === 'P2025') {
      throw new Error(`Product with ID ${id} not found`);
    }
    throw error;
  }
}
```

## 3. API Routes

```typescript
import express from 'express';
import * as productService from './productService';

const router = express.Router();

// Create product
router.post('/products', async (req, res) => {
  try {
    const result = await productService.createProduct(req.body);
    res.status(201).json(result);
  } catch (error: any) {
    if (error.name === 'ZodError') {
      return res.status(400).json({ error: 'Bad Request', details: error.errors });
    }
    if (error.message.includes('already exists')) {
      return res.status(409).json({ error: 'Conflict', message: error.message });
    }
    res.status(500).json({ error: 'Internal Server Error' });
  }
});

// Get product by ID
router.get('/products/:id', async (req, res) => {
  try {
    const result = await productService.getProductById(parseInt(req.params.id));
    res.json(result);
  } catch (error: any) {
    if (error.message.includes('not found')) {
      return res.status(404).json({ error: 'Not Found', message: error.message });
    }
    res.status(500).json({ error: 'Internal Server Error' });
  }
});

// List products
router.get('/products', async (req, res) => {
  try {
    const result = await productService.listProducts(req.query);
    res.json(result);
  } catch (error: any) {
    if (error.name === 'ZodError') {
      return res.status(400).json({ error: 'Bad Request', details: error.errors });
    }
    res.status(500).json({ error: 'Internal Server Error' });
  }
});

// Update product
router.patch('/products/:id', async (req, res) => {
  try {
    const result = await productService.updateProduct(parseInt(req.params.id), req.body);
    res.json(result);
  } catch (error: any) {
    if (error.name === 'ZodError') {
      return res.status(400).json({ error: 'Bad Request', details: error.errors });
    }
    if (error.message.includes('not found')) {
      return res.status(404).json({ error: 'Not Found', message: error.message });
    }
    if (error.message.includes('already exists')) {
      return res.status(409).json({ error: 'Conflict', message: error.message });
    }
    res.status(500).json({ error: 'Internal Server Error' });
  }
});

// Delete product
router.delete('/products/:id', async (req, res) => {
  try {
    const result = await productService.deleteProduct(parseInt(req.params.id));
    res.json(result);
  } catch (error: any) {
    if (error.message.includes('not found')) {
      return res.status(404).json({ error: 'Not Found', message: error.message });
    }
    res.status(500).json({ error: 'Internal Server Error' });
  }
});

export default router;
```

## 4. Request/Response Examples

### Create Product
```http
POST /products
Content-Type: application/json

{
  "name": "Wireless Mouse",
  "description": "Ergonomic wireless mouse with USB receiver",
  "price": 29.99,
  "stock": 50,
  "category": "Electronics"
}

Response (201 Created):
{
  "success": true,
  "data": {
    "id": 1,
    "name": "Wireless Mouse",
    "description": "Ergonomic wireless mouse with USB receiver",
    "price": 29.99,
    "stock": 50,
    "category": "Electronics",
    "createdAt": "2024-02-09T10:00:00Z",
    "updatedAt": "2024-02-09T10:00:00Z"
  }
}
```

### List Products (with filters)
```http
GET /products?category=Electronics&minPrice=10&limit=10&sortBy=price&sortOrder=asc

Response (200 OK):
{
  "success": true,
  "data": [
    { "id": 1, "name": "Wireless Mouse", "price": 29.99, ... }
  ],
  "pagination": {
    "total": 15,
    "limit": 10,
    "offset": 0,
    "hasMore": true
  }
}
```

## 5. Notes
- **Validation**: Uses Zod for runtime type checking and validation
- **Security**: All inputs validated before database operations
- **Performance**: Indexes on category and createdAt for faster queries
- **Pagination**: Supports both limit/offset and provides hasMore flag
- **Error Handling**: Specific error codes for unique constraint, not found, etc.
- **Transactions**: Not needed for single-entity CRUD, but add for complex operations
- **Soft Delete**: Can be added by adding `deletedAt` field and filtering in queries

---

## Usage Notes

- **When to use**: Scaffolding new resources, building REST APIs
- **When NOT to use**: Complex business logic requiring custom implementations
- **Best practice**: Add integration tests for all CRUD operations
- **Iteration**: Customize validation rules and error messages per entity

---

## Quality Checklist

- [ ] Database model includes all fields with proper types
- [ ] Indexes added for commonly queried fields
- [ ] All CRUD operations implemented (Create, Read, Update, Delete)
- [ ] Validation schemas defined for all inputs
- [ ] Error handling covers 400, 404, 409, 500
- [ ] Pagination and filtering in list endpoint
- [ ] API routes map to CRUD functions correctly
- [ ] Examples provided for all operations
