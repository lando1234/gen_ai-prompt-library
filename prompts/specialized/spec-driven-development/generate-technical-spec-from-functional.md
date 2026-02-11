---
type: specialized
complexity: advanced
context: hybrid
output_format: structured
interaction_mode: single-shot
production_ready: experimental
tags: [sdd, technical-spec, architecture, system-design, api-design]
---

# Generate Technical Specification from Functional Spec

## Purpose
Transform functional specifications into detailed technical specifications with architecture decisions, API contracts, data models, and implementation guidelines.

## Context
Use this prompt when:
- Converting approved functional specs into technical designs
- Need detailed technical blueprint before coding
- Coordinating work across multiple developers
- Documenting architectural decisions

---

## Prompt

You are a software architect specializing in Spec-Driven Development. Generate a comprehensive technical specification from a functional specification.

**INPUT**:
- Functional specification document
- Technology stack (languages, frameworks, databases)
- Optional: Architectural constraints, performance requirements

**GENERATE**:

1. **System Architecture**:
   - High-level component diagram
   - Service boundaries and responsibilities
   - Integration points (APIs, queues, databases)
   - Technology choices with rationale

2. **API Specifications**:
   - Endpoint definitions (REST/GraphQL)
   - Request/response schemas (JSON/Protocol Buffers)
   - Authentication/authorization mechanism
   - Error response format
   - Versioning strategy

3. **Data Model**:
   - Database schema (tables, columns, indexes)
   - Entity relationships (ER diagram)
   - Data validation rules
   - Migration strategy

4. **Component Design**:
   - Module structure and organization
   - Class diagrams (for OOP)
   - Interface contracts
   - Dependency injection strategy

5. **Implementation Guidelines**:
   - Coding patterns to follow
   - Error handling approach
   - Logging and monitoring strategy
   - Testing strategy (unit, integration, E2E)

6. **Non-Functional Requirements**:
   - Performance targets (latency, throughput)
   - Scalability approach (horizontal/vertical)
   - Security measures (encryption, sanitization)
   - Deployment strategy

7. **Dependencies & Integrations**:
   - External APIs and SDKs
   - Third-party libraries
   - Infrastructure requirements
   - Service-level agreements (SLAs)

**OUTPUT FORMAT**:
```markdown
# Technical Specification: [Feature Name]

## 1. Overview

**Feature**: [Name]
**Functional Spec Reference**: [Link or ID]
**Tech Stack**: [Languages/Frameworks/Databases]
**Estimated Effort**: [Person-days]

## 2. System Architecture

### High-Level Diagram
```
[ASCII/Mermaid diagram showing components]
```

### Components
| Component | Responsibility | Technology | Justification |
|-----------|---------------|------------|---------------|
| [...] | | | |

### Data Flow
1. [Step 1 with data transformations]
2. [Step 2...]

## 3. API Specification

### Endpoints

#### POST /api/v1/resource
**Description**: [What it does]

**Request**:
```json
{
  "field": "type",
  "field2": "type"
}
```

**Response (200)**:
```json
{
  "id": "string",
  "field": "value"
}
```

**Errors**:
- 400: [Validation error structure]
- 401: [Authentication error]
- 500: [Server error]

**Rate Limiting**: [X requests/minute]

### Authentication
[JWT/OAuth2/API Key strategy]

## 4. Data Model

### Database Schema

#### Table: `users`
| Column | Type | Constraints | Index | Notes |
|--------|------|-------------|-------|-------|
| id | UUID | PRIMARY KEY | Yes | Generated |
| email | VARCHAR(254) | UNIQUE, NOT NULL | Yes | Lowercase |
| [...] | | | | |

#### Relationships
```
users (1) ──< (N) orders
orders (N) ──> (1) products
```

### Indexes
- `idx_users_email` - B-tree on users.email (login performance)
- `idx_orders_user_id` - B-tree on orders.user_id (user orders lookup)

### Migrations
- Use [Flyway/Liquibase/Alembic] for versioned migrations
- Backward compatible changes only (no breaking schema changes)

## 5. Component Design

### Module Structure
```
src/
├── api/
│   ├── routes/
│   ├── controllers/
│   └── middleware/
├── services/
│   ├── UserService.ts
│   └── OrderService.ts
├── repositories/
│   ├── UserRepository.ts
│   └── OrderRepository.ts
├── models/
│   ├── User.ts
│   └── Order.ts
└── utils/
    ├── validators.ts
    └── errors.ts
```

### Key Classes/Interfaces

#### UserService
```typescript
interface IUserService {
  createUser(data: UserCreateDTO): Promise<User>;
  getUserById(id: string): Promise<User | null>;
  updateUser(id: string, data: UserUpdateDTO): Promise<User>;
  deleteUser(id: string): Promise<void>;
}
```

**Responsibilities**:
- Business logic validation
- Orchestrates repository calls
- Emits domain events

#### UserRepository
```typescript
interface IUserRepository {
  save(user: User): Promise<User>;
  findById(id: string): Promise<User | null>;
  findByEmail(email: string): Promise<User | null>;
}
```

**Responsibilities**:
- Database operations only
- No business logic
- Returns domain entities

### Design Patterns
- **Repository Pattern**: Data access abstraction
- **Service Layer**: Business logic encapsulation
- **DTO Pattern**: API request/response objects separate from domain models
- **Dependency Injection**: Constructor injection for testability

## 6. Implementation Guidelines

### Error Handling
```typescript
class AppError extends Error {
  statusCode: number;
  isOperational: boolean;

  constructor(message: string, statusCode: number) {
    super(message);
    this.statusCode = statusCode;
    this.isOperational = true;
  }
}

// Usage
if (!user) {
  throw new AppError('User not found', 404);
}
```

**Strategy**:
- Operational errors (404, 400) → AppError with clear message
- Programming errors → Let crash and restart (fail-fast)
- All async functions use try/catch
- Global error handler in middleware

### Logging
```typescript
logger.info('User created', {
  userId: user.id,
  email: user.email,
  timestamp: new Date().toISOString()
});
```

**Levels**: ERROR (crashes), WARN (recoverable), INFO (business events), DEBUG (troubleshooting)
**Format**: Structured JSON for log aggregation (ELK/Splunk)

### Validation
- Input validation: At API boundary (controllers) using Zod/Joi
- Business validation: In service layer
- Database constraints: Enforced at schema level

### Testing Strategy
- **Unit Tests**: Services, utilities (80%+ coverage)
- **Integration Tests**: API endpoints with test database
- **E2E Tests**: Critical user flows (happy paths)
- **Mocking**: External APIs, database for unit tests

## 7. Non-Functional Requirements

### Performance
- **API Latency**: P95 < 200ms, P99 < 500ms
- **Database Queries**: All queries < 100ms (use EXPLAIN ANALYZE)
- **Throughput**: 1000 requests/second per instance
- **Caching**: Redis for frequently accessed data (TTL: 5 minutes)

### Scalability
- **Horizontal Scaling**: Stateless API, scale via load balancer
- **Database**: Read replicas for queries, write to primary
- **Queue**: Use message queue (RabbitMQ/SQS) for async tasks

### Security
- **Authentication**: JWT tokens (15-minute expiry, refresh tokens)
- **Authorization**: Role-based access control (RBAC)
- **Input Sanitization**: All user input validated and escaped
- **SQL Injection**: Use parameterized queries only
- **XSS Prevention**: Content Security Policy headers
- **Rate Limiting**: 100 requests/minute per IP

### Deployment
- **Environment**: Docker containers on Kubernetes
- **CI/CD**: GitHub Actions → build → test → deploy to staging → manual prod approval
- **Rollback**: Blue/green deployment, instant rollback via service switch
- **Health Checks**: `/health` endpoint (DB connection, dependencies)

## 8. Dependencies & Integrations

### External Services
| Service | Purpose | Fallback Strategy | SLA |
|---------|---------|-------------------|-----|
| Stripe API | Payments | Queue for retry | 99.9% uptime |
| SendGrid | Email | Queue with 24h retry | 99.99% uptime |
| [...] | | | |

### Libraries
- **Express**: Web framework (v4.18+)
- **TypeORM**: Database ORM (v0.3+)
- **Zod**: Schema validation (v3.22+)
- **Jest**: Testing framework (v29+)

### Infrastructure
- **Database**: PostgreSQL 15 (2 vCPU, 8GB RAM, 100GB SSD)
- **Cache**: Redis 7 (2GB RAM)
- **Queue**: RabbitMQ (3 nodes for HA)
- **Load Balancer**: AWS ALB

## 9. Implementation Phases

### Phase 1: Foundation (Week 1)
- [ ] Set up project structure
- [ ] Configure database and migrations
- [ ] Implement authentication middleware
- [ ] Create base error handling

### Phase 2: Core Features (Week 2-3)
- [ ] User management API
- [ ] Order creation flow
- [ ] Payment integration
- [ ] Email notifications

### Phase 3: Testing & Optimization (Week 4)
- [ ] Write comprehensive tests
- [ ] Performance testing and optimization
- [ ] Security audit
- [ ] Documentation

## 10. Open Questions

- **Concurrency**: How do we handle concurrent order creation for same product?
- **Data Retention**: How long do we keep deleted user data (GDPR compliance)?
- **Monitoring**: Which metrics dashboard tool (Grafana/Datadog)?

---

## Assumptions

- Single region deployment initially (expand to multi-region in Phase 2)
- PostgreSQL sufficient for 1M users (evaluate sharding at 10M)
- Synchronous payment processing (evaluate async queue if latency issues)

---

## Risk Assessment

| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| Stripe API downtime | High | Low | Queue payments with retry logic |
| Database performance | High | Medium | Optimize queries, add indexes, read replicas |
| Security breach | Critical | Low | Regular audits, penetration testing |
```

**CONSTRAINTS**:
- All technical decisions must be justified
- Include concrete code examples (not pseudocode)
- Specify versions for all dependencies
- Address scalability and security explicitly
- Provide implementation timeline
- Document assumptions and risks

---

## Example

**Input Functional Spec**: Discount Code Application (from previous prompt)

**Output**:

```markdown
# Technical Specification: Discount Code Application

## 1. Overview

**Feature**: Discount Code Application at Checkout
**Functional Spec Reference**: FS-2024-001
**Tech Stack**:
- Backend: Node.js 20 + Express + TypeScript
- Database: PostgreSQL 15
- Cache: Redis 7
- Frontend: React 18 (separate spec)
**Estimated Effort**: 12 person-days

## 2. System Architecture

### High-Level Diagram
```
┌─────────────┐
│   Client    │
│  (Browser)  │
└──────┬──────┘
       │ HTTPS
       ▼
┌─────────────────────────────────────┐
│         API Gateway                 │
│  (Rate Limiting + Auth)             │
└──────┬────────────────────┬─────────┘
       │                    │
       ▼                    ▼
┌──────────────┐     ┌──────────────┐
│   Discount   │     │    Cart      │
│   Service    │     │   Service    │
└──────┬───────┘     └──────┬───────┘
       │                    │
       ▼                    ▼
┌──────────────┐     ┌──────────────┐
│  PostgreSQL  │     │  PostgreSQL  │
│  (discounts) │     │   (carts)    │
└──────────────┘     └──────────────┘
       │
       ▼
┌──────────────┐
│    Redis     │
│  (Cache)     │
└──────────────┘
```

### Components

| Component | Responsibility | Technology | Justification |
|-----------|---------------|------------|---------------|
| API Gateway | Auth, rate limiting, routing | Express middleware | Centralized security, simple setup |
| Discount Service | Discount validation & calculation | TypeScript + Express | Type safety, async/await support |
| Cart Service | Cart total calculation | TypeScript + Express | Existing service, integration |
| PostgreSQL | Persistent storage | PostgreSQL 15 | ACID compliance, complex queries |
| Redis | Discount code caching | Redis 7 | Fast lookups (O(1)), reduce DB load |

### Data Flow: Apply Discount Code

1. **Client** → POST `/api/v1/cart/discount` with `{ code: "SAVE20", cartId: "uuid" }`
2. **API Gateway** → Authenticate user, rate limit (5 attempts/min)
3. **Discount Service** → Check Redis cache for code
   - Cache HIT → Use cached discount
   - Cache MISS → Query PostgreSQL, cache result (TTL: 5 min)
4. **Discount Service** → Validate code (exists, not expired, meets conditions)
5. **Cart Service** → Fetch cart total
6. **Discount Service** → Calculate discount amount
7. **Discount Service** → Return `{ discountAmount, newTotal, message }`
8. **Client** → Update UI with discount applied

## 3. API Specification

### Endpoints

#### POST /api/v1/cart/discount
**Description**: Apply discount code to cart

**Headers**:
```
Authorization: Bearer <jwt_token>
Content-Type: application/json
```

**Request**:
```json
{
  "code": "SAVE20",
  "cartId": "550e8400-e29b-41d4-a716-446655440000"
}
```

**Validation**:
- `code`: 3-20 alphanumeric chars, required
- `cartId`: Valid UUID, required

**Response (200 - Success)**:
```json
{
  "success": true,
  "data": {
    "discountId": "d123-456",
    "code": "SAVE20",
    "discountType": "percentage",
    "discountValue": 20,
    "discountAmount": 20.00,
    "originalTotal": 100.00,
    "newTotal": 80.00,
    "message": "Discount applied: 20% off"
  }
}
```

**Response (400 - Invalid Code)**:
```json
{
  "success": false,
  "error": {
    "code": "INVALID_DISCOUNT_CODE",
    "message": "Invalid discount code",
    "field": "code"
  }
}
```

**Response (400 - Expired)**:
```json
{
  "success": false,
  "error": {
    "code": "DISCOUNT_EXPIRED",
    "message": "This discount code has expired",
    "field": "code",
    "meta": {
      "expiredAt": "2024-01-15T00:00:00Z"
    }
  }
}
```

**Response (400 - Minimum Not Met)**:
```json
{
  "success": false,
  "error": {
    "code": "MINIMUM_PURCHASE_NOT_MET",
    "message": "Minimum purchase of $100 required",
    "field": "code",
    "meta": {
      "minimumRequired": 100.00,
      "currentTotal": 75.00
    }
  }
}
```

**Response (429 - Rate Limit)**:
```json
{
  "success": false,
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "Too many discount code attempts. Try again in 1 minute.",
    "retryAfter": 60
  }
}
```

**Rate Limiting**: 5 attempts/minute per user

#### DELETE /api/v1/cart/discount
**Description**: Remove applied discount from cart

**Request**:
```json
{
  "cartId": "550e8400-e29b-41d4-a716-446655440000"
}
```

**Response (200)**:
```json
{
  "success": true,
  "data": {
    "originalTotal": 80.00,
    "newTotal": 100.00,
    "message": "Discount removed"
  }
}
```

### Authentication
- **Method**: JWT tokens (Bearer scheme)
- **Token Expiry**: 15 minutes
- **Refresh**: Refresh token (7 days)
- **Payload**: `{ userId, email, role }`

## 4. Data Model

### Database Schema

#### Table: `discount_codes`
| Column | Type | Constraints | Index | Notes |
|--------|------|-------------|-------|-------|
| id | UUID | PRIMARY KEY | Yes | Auto-generated |
| code | VARCHAR(20) | UNIQUE, NOT NULL | Yes (B-tree) | Uppercase |
| discount_type | ENUM('percentage', 'fixed') | NOT NULL | No | Calculation method |
| discount_value | DECIMAL(10,2) | NOT NULL | No | 20 or 20.00 |
| min_purchase | DECIMAL(10,2) | NULL | No | Minimum cart total |
| max_uses | INTEGER | NULL | No | Global usage limit |
| max_uses_per_user | INTEGER | DEFAULT 1 | No | Per-user limit |
| valid_from | TIMESTAMP | NOT NULL | No | Start date |
| valid_until | TIMESTAMP | NOT NULL | Yes (B-tree) | Expiry date |
| is_active | BOOLEAN | DEFAULT TRUE | Yes | Soft delete flag |
| created_at | TIMESTAMP | DEFAULT NOW() | No | Audit |
| updated_at | TIMESTAMP | DEFAULT NOW() | No | Audit |

#### Table: `discount_usage`
| Column | Type | Constraints | Index | Notes |
|--------|------|-------------|-------|-------|
| id | UUID | PRIMARY KEY | Yes | |
| discount_id | UUID | FK → discount_codes.id | Yes (B-tree) | |
| user_id | UUID | FK → users.id | Yes (composite) | |
| order_id | UUID | FK → orders.id | Yes | |
| applied_at | TIMESTAMP | DEFAULT NOW() | No | |

**Composite Index**: `idx_discount_usage_user` on (discount_id, user_id) for per-user limit checks

#### Table: `carts` (existing)
| Column | Type | Constraints | Index | Notes |
|--------|------|-------------|-------|-------|
| id | UUID | PRIMARY KEY | Yes | |
| user_id | UUID | FK → users.id | Yes | |
| applied_discount_id | UUID | FK → discount_codes.id, NULL | No | Current discount |
| discount_amount | DECIMAL(10,2) | NULL | No | Cached calculation |
| subtotal | DECIMAL(10,2) | NOT NULL | No | Before discount |
| total | DECIMAL(10,2) | NOT NULL | No | After discount |
| updated_at | TIMESTAMP | DEFAULT NOW() | No | |

### Relationships
```
discount_codes (1) ──< (N) discount_usage
discount_usage (N) ──> (1) users
discount_usage (N) ──> (1) orders
carts (N) ──> (1) discount_codes [optional]
```

### Indexes Rationale
- `discount_codes.code`: Fast O(1) lookup on apply
- `discount_codes.valid_until`: Efficient cleanup of expired codes
- `discount_usage (discount_id, user_id)`: Check per-user usage limit
- `discount_codes.is_active`: Filter active codes

### Migrations
**Migration 001**: Create discount_codes table
```sql
CREATE TYPE discount_type AS ENUM ('percentage', 'fixed');

CREATE TABLE discount_codes (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  code VARCHAR(20) UNIQUE NOT NULL,
  discount_type discount_type NOT NULL,
  discount_value DECIMAL(10,2) NOT NULL CHECK (discount_value > 0),
  min_purchase DECIMAL(10,2) CHECK (min_purchase >= 0),
  max_uses INTEGER CHECK (max_uses > 0),
  max_uses_per_user INTEGER DEFAULT 1 CHECK (max_uses_per_user > 0),
  valid_from TIMESTAMP NOT NULL,
  valid_until TIMESTAMP NOT NULL CHECK (valid_until > valid_from),
  is_active BOOLEAN DEFAULT TRUE,
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

CREATE INDEX idx_discount_codes_code ON discount_codes(code);
CREATE INDEX idx_discount_codes_valid_until ON discount_codes(valid_until);
CREATE INDEX idx_discount_codes_is_active ON discount_codes(is_active);
```

## 5. Component Design

### Module Structure
```
src/
├── api/
│   ├── routes/
│   │   ├── discount.routes.ts
│   │   └── cart.routes.ts
│   ├── controllers/
│   │   └── DiscountController.ts
│   └── middleware/
│       ├── auth.middleware.ts
│       └── rateLimit.middleware.ts
├── services/
│   ├── DiscountService.ts
│   ├── CartService.ts
│   └── CacheService.ts
├── repositories/
│   └── DiscountRepository.ts
├── models/
│   ├── Discount.ts
│   └── Cart.ts
├── validators/
│   └── discountSchemas.ts
└── utils/
    ├── errors.ts
    └── logger.ts
```

### Key Classes/Interfaces

#### DiscountService
```typescript
interface IDiscountService {
  applyDiscount(code: string, cartId: string, userId: string): Promise<DiscountResult>;
  removeDiscount(cartId: string, userId: string): Promise<void>;
  validateDiscount(code: string, cartTotal: number, userId: string): Promise<Discount>;
  calculateDiscountAmount(discount: Discount, cartTotal: number): number;
}

interface DiscountResult {
  discountId: string;
  code: string;
  discountType: 'percentage' | 'fixed';
  discountValue: number;
  discountAmount: number;
  originalTotal: number;
  newTotal: number;
  message: string;
}
```

**Responsibilities**:
- Validate discount code (exists, not expired, usage limits)
- Calculate discount amount
- Check cart against minimum purchase requirement
- Track usage in discount_usage table
- Orchestrate cache + database operations

#### DiscountRepository
```typescript
interface IDiscountRepository {
  findByCode(code: string): Promise<Discount | null>;
  incrementUsage(discountId: string, userId: string, orderId: string): Promise<void>;
  getUserUsageCount(discountId: string, userId: string): Promise<number>;
  getTotalUsageCount(discountId: string): Promise<number>;
}
```

**Responsibilities**:
- Database CRUD for discount_codes
- Query discount_usage for limit checks
- Atomic usage tracking (prevent race conditions)

#### CacheService
```typescript
interface ICacheService {
  get<T>(key: string): Promise<T | null>;
  set<T>(key: string, value: T, ttl?: number): Promise<void>;
  delete(key: string): Promise<void>;
  generateKey(namespace: string, identifier: string): string;
}
```

**Usage**:
```typescript
// Cache key: "discount:code:SAVE20"
const cached = await cacheService.get<Discount>('discount:code:SAVE20');
if (!cached) {
  const discount = await discountRepo.findByCode('SAVE20');
  await cacheService.set('discount:code:SAVE20', discount, 300); // 5 min TTL
}
```

### Design Patterns
- **Repository Pattern**: Abstract database operations
- **Service Layer**: Business logic encapsulation
- **Dependency Injection**: Constructor injection for testability
- **Strategy Pattern**: Different discount calculations (percentage vs fixed)
- **Cache-Aside**: Check cache → miss → fetch DB → populate cache

## 6. Implementation Guidelines

### Error Handling
```typescript
export class DiscountError extends AppError {
  constructor(
    message: string,
    public code: string,
    public field?: string,
    public meta?: Record<string, any>
  ) {
    super(message, 400);
  }
}

// Usage in service
if (discount.valid_until < new Date()) {
  throw new DiscountError(
    'This discount code has expired',
    'DISCOUNT_EXPIRED',
    'code',
    { expiredAt: discount.valid_until }
  );
}
```

**Error Codes**:
- `INVALID_DISCOUNT_CODE`: Code doesn't exist or inactive
- `DISCOUNT_EXPIRED`: Past valid_until date
- `MINIMUM_PURCHASE_NOT_MET`: Cart total < min_purchase
- `USAGE_LIMIT_REACHED`: max_uses or max_uses_per_user exceeded
- `DISCOUNT_ALREADY_APPLIED`: User tries to apply second discount

### Logging
```typescript
logger.info('Discount applied', {
  userId,
  cartId,
  discountCode: discount.code,
  discountAmount: result.discountAmount,
  originalTotal,
  newTotal,
  timestamp: new Date().toISOString()
});

logger.warn('Discount validation failed', {
  userId,
  code,
  reason: 'MINIMUM_PURCHASE_NOT_MET',
  required: discount.min_purchase,
  actual: cartTotal
});
```

### Validation
```typescript
import { z } from 'zod';

export const applyDiscountSchema = z.object({
  code: z.string()
    .min(3, 'Code must be at least 3 characters')
    .max(20, 'Code must not exceed 20 characters')
    .regex(/^[A-Z0-9]+$/, 'Code must be uppercase alphanumeric')
    .transform(str => str.toUpperCase()),
  cartId: z.string().uuid('Invalid cart ID')
});
```

### Testing Strategy

**Unit Tests** (DiscountService):
```typescript
describe('DiscountService.validateDiscount', () => {
  it('should validate valid percentage discount', async () => {
    const discount = await service.validateDiscount('SAVE20', 100, 'user-123');
    expect(discount.code).toBe('SAVE20');
  });

  it('should throw DISCOUNT_EXPIRED for expired code', async () => {
    await expect(
      service.validateDiscount('EXPIRED', 100, 'user-123')
    ).rejects.toThrow(DiscountError);
  });

  it('should throw MINIMUM_PURCHASE_NOT_MET when below minimum', async () => {
    await expect(
      service.validateDiscount('BIG50', 50, 'user-123') // min $100
    ).rejects.toThrow('Minimum purchase');
  });
});
```

**Integration Tests** (API endpoint):
```typescript
describe('POST /api/v1/cart/discount', () => {
  it('should apply valid discount code', async () => {
    const res = await request(app)
      .post('/api/v1/cart/discount')
      .set('Authorization', `Bearer ${token}`)
      .send({ code: 'SAVE20', cartId: testCartId })
      .expect(200);

    expect(res.body.success).toBe(true);
    expect(res.body.data.discountAmount).toBe(20);
    expect(res.body.data.newTotal).toBe(80);
  });

  it('should return 429 after 5 failed attempts', async () => {
    // Attempt 5 times with invalid code
    for (let i = 0; i < 5; i++) {
      await request(app)
        .post('/api/v1/cart/discount')
        .set('Authorization', `Bearer ${token}`)
        .send({ code: 'INVALID', cartId: testCartId });
    }

    // 6th attempt should be rate limited
    const res = await request(app)
      .post('/api/v1/cart/discount')
      .set('Authorization', `Bearer ${token}`)
      .send({ code: 'SAVE20', cartId: testCartId })
      .expect(429);

    expect(res.body.error.code).toBe('RATE_LIMIT_EXCEEDED');
  });
});
```

## 7. Non-Functional Requirements

### Performance
- **API Latency**:
  - P50: <50ms (cache hit)
  - P95: <150ms (cache miss, DB query)
  - P99: <300ms
- **Database Queries**:
  - Discount lookup: <10ms (indexed on code)
  - Usage count: <20ms (composite index on discount_id, user_id)
- **Cache Hit Rate**: Target >90% for discount code lookups
- **Throughput**: 500 discount applications/second per instance

**Optimization**:
- Redis caching with 5-minute TTL
- Database connection pooling (max 20 connections)
- Query optimization: `EXPLAIN ANALYZE` on all queries

### Scalability
- **Horizontal Scaling**: Stateless API, deploy 3+ instances behind load balancer
- **Database**: Read replica for usage count queries (eventually consistent OK)
- **Cache**: Redis cluster (3 nodes) for high availability
- **Race Condition Handling**: Use database transactions for usage tracking

### Security
- **Authentication**: JWT tokens, validate on every request
- **Authorization**: Users can only apply discounts to their own carts
- **Input Sanitization**: Validate all input with Zod schemas
- **SQL Injection**: Use parameterized queries (TypeORM protects by default)
- **Rate Limiting**: 5 attempts/minute per user (prevent brute-force code guessing)
- **Audit Logging**: Log all discount applications for fraud detection

**Security Measure**: Prevent brute-force discount code guessing
```typescript
// Rate limit middleware
const discountRateLimiter = rateLimit({
  windowMs: 60 * 1000, // 1 minute
  max: 5, // 5 attempts
  message: 'Too many discount code attempts',
  standardHeaders: true,
  legacyHeaders: false,
  keyGenerator: (req) => req.user.id // Per-user rate limit
});
```

### Deployment
- **Environment**: Docker + Kubernetes
- **CI/CD**:
  1. GitHub Actions triggers on push to `main`
  2. Run tests (unit + integration)
  3. Build Docker image
  4. Deploy to staging → smoke tests
  5. Manual approval → deploy to production
- **Rollback**: Blue/green deployment, instant switch to previous version
- **Health Check**: `GET /health` checks DB + Redis connectivity

## 8. Dependencies & Integrations

### External Services
| Service | Purpose | Fallback | SLA |
|---------|---------|----------|-----|
| Cart Service | Fetch cart total | Return 503 if down | 99.9% |

### Libraries
- **express**: ^4.18.0 - Web framework
- **typeorm**: ^0.3.0 - Database ORM
- **redis**: ^4.6.0 - Caching client
- **zod**: ^3.22.0 - Schema validation
- **jsonwebtoken**: ^9.0.0 - JWT authentication
- **express-rate-limit**: ^6.10.0 - Rate limiting
- **winston**: ^3.11.0 - Logging

### Infrastructure
- **Database**: PostgreSQL 15 (AWS RDS: db.t3.medium - 2 vCPU, 4GB RAM)
- **Cache**: Redis 7 (AWS ElastiCache: cache.t3.small - 2GB RAM)
- **Load Balancer**: AWS ALB (Application Load Balancer)

## 9. Implementation Phases

### Phase 1: Database & Models (Day 1-2)
- [ ] Create migration for discount_codes table
- [ ] Create migration for discount_usage table
- [ ] Implement Discount domain model
- [ ] Implement DiscountRepository

### Phase 2: Core Service Logic (Day 3-5)
- [ ] Implement DiscountService.validateDiscount
- [ ] Implement DiscountService.calculateDiscountAmount
- [ ] Implement DiscountService.applyDiscount
- [ ] Implement CacheService with Redis
- [ ] Write unit tests (TDD approach)

### Phase 3: API Endpoints (Day 6-7)
- [ ] Implement POST /api/v1/cart/discount
- [ ] Implement DELETE /api/v1/cart/discount
- [ ] Add authentication middleware
- [ ] Add rate limiting middleware
- [ ] Write integration tests

### Phase 4: Integration & Testing (Day 8-10)
- [ ] Integrate with Cart Service
- [ ] End-to-end testing
- [ ] Performance testing (load test with 1000 req/s)
- [ ] Security audit (OWASP Top 10)

### Phase 5: Deployment (Day 11-12)
- [ ] Deploy to staging environment
- [ ] Smoke tests in staging
- [ ] Production deployment
- [ ] Monitor metrics (latency, error rate, cache hit rate)

## 10. Open Questions

❓ **Concurrency**: What happens if two users try to use the last available discount (max_uses = 1000, current = 999)?
- **Answer**: Use database transaction with row-level lock on discount_codes table

❓ **Cart Modification**: If user applies discount then removes items (cart total drops below minimum), do we auto-remove discount?
- **Proposed**: Yes, re-validate on cart update, remove discount if invalid

❓ **Expired Code Cleanup**: How do we archive/delete expired discount codes?
- **Proposed**: Daily cron job sets `is_active = false` for `valid_until < NOW()`, hard delete after 90 days

---

## Assumptions

- Single-region deployment initially (US-East-1)
- Average cart total: $50-$200
- Peak traffic: 1000 requests/second during Black Friday (scale to 10 instances)
- Discount codes are created manually by admin (no self-service portal in Phase 1)

---

## Risk Assessment

| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| Race condition on usage limits | Medium | Medium | Use database transactions with row locks |
| Cache invalidation issues | Low | Medium | Keep TTL short (5 min), accept eventual consistency |
| Database performance bottleneck | High | Low | Add read replicas, optimize queries, monitor slow query log |
| Discount code leaks | High | Low | Rate limiting, audit logging, fraud detection alerts |
```

---

## Usage Notes

- **When to use**: After functional spec approval, before coding starts
- **When NOT to use**: For trivial features with obvious implementation
- **Best practice**: Review with senior engineers before implementation
- **Iteration**: Adjust based on performance testing and code review feedback

---

## Quality Checklist

- [ ] Architecture diagram showing all components
- [ ] Complete API specifications with examples
- [ ] Database schema with indexes and relationships
- [ ] Code examples for key classes/interfaces
- [ ] Error handling strategy documented
- [ ] Performance targets specified (latency, throughput)
- [ ] Security measures identified
- [ ] Testing strategy defined (unit, integration, E2E)
- [ ] Dependencies and versions listed
- [ ] Implementation phases with timeline
- [ ] Open questions documented
- [ ] Risks assessed with mitigation plans
