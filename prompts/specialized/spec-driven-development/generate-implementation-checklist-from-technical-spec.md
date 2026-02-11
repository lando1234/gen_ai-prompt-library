---
type: specialized
complexity: basic
context: technical
output_format: structured
interaction_mode: single-shot
production_ready: experimental
tags: [sdd, checklist, implementation, task-breakdown, project-management]
---

# Generate Implementation Checklist from Technical Spec

## Purpose
Convert technical specifications into actionable implementation checklists with tasks, subtasks, acceptance criteria, and effort estimates.

## Context
Use this prompt when:
- Starting implementation from approved technical spec
- Need structured task breakdown for team coordination
- Tracking implementation progress
- Estimating development effort

---

## Prompt

You are a technical project manager specializing in task breakdown. Generate a comprehensive implementation checklist from technical specifications.

**INPUT**:
- Technical specification document
- Optional: Team size, sprint duration, skill levels

**GENERATE**:

1. **Task Identification**:
   - Break spec into logical implementation units
   - Group related tasks (database, API, business logic, tests)
   - Identify dependencies between tasks
   - Sequence tasks by dependency order

2. **Task Details**:
   - Clear task title (actionable, specific)
   - Acceptance criteria (what "done" looks like)
   - Effort estimate (hours or story points)
   - Priority (P0 critical, P1 high, P2 medium, P3 low)
   - Dependencies (blockers)

3. **Task Categories**:
   - **Setup**: Project scaffolding, dependencies
   - **Database**: Migrations, models, repositories
   - **Business Logic**: Services, domain logic
   - **API**: Controllers, routes, validation
   - **Testing**: Unit tests, integration tests
   - **Integration**: External services, APIs
   - **Documentation**: API docs, README
   - **Deployment**: Docker, CI/CD, monitoring

4. **Milestones**:
   - Phase 1: Foundation (database, models)
   - Phase 2: Core logic (services, business rules)
   - Phase 3: API layer (endpoints, validation)
   - Phase 4: Testing & QA
   - Phase 5: Deployment & monitoring

5. **Effort Estimation**:
   - Per-task estimates (hours)
   - Phase totals
   - Overall project estimate
   - Buffer for unknowns (20%)

**OUTPUT FORMAT**:
```markdown
# Implementation Checklist: [Feature Name]

## Overview
- **Total Tasks**: [number]
- **Total Effort**: [X person-days]
- **Estimated Duration**: [X weeks with Y developers]
- **Priority Distribution**: P0: X, P1: Y, P2: Z
- **Dependencies**: [X blocking relationships]

---

## Phase 1: Foundation & Setup
**Effort**: [X hours] | **Duration**: [Y days]

### Setup & Configuration

- [ ] **[SETUP-001]** Initialize project repository
  - **Acceptance Criteria**:
    - Git repository created with .gitignore
    - README.md with setup instructions
    - License file added
  - **Effort**: 1 hour
  - **Priority**: P0 (blocks all)
  - **Dependencies**: None
  - **Assignee**: [Developer]

- [ ] **[SETUP-002]** Configure development environment
  - **Acceptance Criteria**:
    - Node.js 20+ installed
    - Docker and Docker Compose configured
    - Environment variables documented in .env.example
  - **Effort**: 2 hours
  - **Priority**: P0
  - **Dependencies**: SETUP-001

### Database Layer

- [ ] **[DB-001]** Create database migration for [table]
  - **Acceptance Criteria**:
    - Migration file with CREATE TABLE statement
    - All columns, constraints, indexes defined
    - UP and DOWN migrations working
  - **Effort**: 3 hours
  - **Priority**: P0
  - **Dependencies**: SETUP-002
  - **Spec Reference**: Technical Spec Section 4.1

---

## Phase 2: Core Business Logic
**Effort**: [X hours] | **Duration**: [Y days]

### Domain Models

- [ ] **[MODEL-001]** Implement [Entity] model
  - **Acceptance Criteria**:
    - TypeScript interface with all fields
    - Validation methods
    - Factory methods for creation
  - **Effort**: 2 hours
  - **Priority**: P0
  - **Dependencies**: DB-001

### Repositories

- [ ] **[REPO-001]** Implement [Entity]Repository
  - **Acceptance Criteria**:
    - CRUD methods (create, findById, findAll, update, delete)
    - Query methods per technical spec
    - Error handling for database failures
  - **Effort**: 4 hours
  - **Priority**: P0
  - **Dependencies**: MODEL-001

### Services

- [ ] **[SERVICE-001]** Implement [Feature]Service
  - **Acceptance Criteria**:
    - All business methods per technical spec
    - Validation logic implemented
    - Error handling with custom exceptions
  - **Effort**: 6 hours
  - **Priority**: P0
  - **Dependencies**: REPO-001
  - **Spec Reference**: Technical Spec Section 5.2

---

## Phase 3: API Layer
**Effort**: [X hours] | **Duration**: [Y days]

### Controllers

- [ ] **[API-001]** Implement POST /api/v1/resource endpoint
  - **Acceptance Criteria**:
    - Request validation with Zod/Joi
    - Calls service layer
    - Returns spec-compliant response
    - Error handling with proper status codes
  - **Effort**: 3 hours
  - **Priority**: P0
  - **Dependencies**: SERVICE-001
  - **Spec Reference**: API Spec - POST /resource

---

## Phase 4: Testing
**Effort**: [X hours] | **Duration**: [Y days]

### Unit Tests

- [ ] **[TEST-001]** Write unit tests for [Service]
  - **Acceptance Criteria**:
    - 80%+ code coverage
    - All public methods tested
    - Edge cases covered
  - **Effort**: 4 hours
  - **Priority**: P1
  - **Dependencies**: SERVICE-001

---

## Phase 5: Deployment
**Effort**: [X hours] | **Duration**: [Y days]

- [ ] **[DEPLOY-001]** Create Dockerfile
  - **Acceptance Criteria**:
    - Multi-stage build
    - Production-ready image
    - Health check endpoint
  - **Effort**: 2 hours
  - **Priority**: P1
  - **Dependencies**: All core features complete

---

## Milestones

### Milestone 1: Database Ready (Day 3)
- [ ] All migrations created
- [ ] All models implemented
- [ ] All repositories working

### Milestone 2: Business Logic Complete (Day 7)
- [ ] All services implemented
- [ ] Business rules enforced
- [ ] Unit tests passing

### Milestone 3: API Functional (Day 10)
- [ ] All endpoints implemented
- [ ] Integration tests passing
- [ ] OpenAPI spec validated

### Milestone 4: Production Ready (Day 12)
- [ ] All tests passing
- [ ] Docker image built
- [ ] CI/CD pipeline working
- [ ] Monitoring configured

---

## Effort Summary

| Phase | Tasks | Hours | Days (1 dev) |
|-------|-------|-------|--------------|
| Phase 1: Foundation | 5 | 15 | 2 |
| Phase 2: Core Logic | 8 | 40 | 5 |
| Phase 3: API Layer | 6 | 24 | 3 |
| Phase 4: Testing | 5 | 20 | 2.5 |
| Phase 5: Deployment | 3 | 8 | 1 |
| **TOTAL** | **27** | **107** | **13.5** |

**With Buffer (20%)**: 128 hours / 16 days (1 developer)
**With 2 Developers**: ~8-10 days (accounting for coordination overhead)

---

## Dependencies Graph

```
SETUP-001 → SETUP-002 → DB-001 → MODEL-001 → REPO-001 → SERVICE-001 → API-001
                                                                     ↓
                                                                 TEST-001
```

**Critical Path**: SETUP-001 → SETUP-002 → DB-001 → MODEL-001 → REPO-001 → SERVICE-001 → API-001
**Duration**: 23 hours (3 days)

---

## Risk Assessment

| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|------------|
| Database schema changes | Medium | High | Freeze schema early, version migrations |
| Third-party API downtime | Low | Medium | Implement retry logic, use test mocks |
| Scope creep | High | High | Strict change control, update spec before coding |
| Key person unavailability | Low | High | Cross-train team, document decisions |

---

## Daily Progress Tracking

### Day 1
- [ ] SETUP-001, SETUP-002 complete
- [ ] DB-001, DB-002 complete

### Day 2
- [ ] MODEL-001, MODEL-002 complete
- [ ] REPO-001 started

[... continue for all days]

---

## Notes
- Review technical spec before starting each phase
- Update checklist as new tasks discovered
- Mark blockers immediately in standup
- Celebrate milestone completions
```

**CONSTRAINTS**:
- Each task must be actionable and specific
- Acceptance criteria must be verifiable
- Effort estimates realistic (not overly optimistic)
- Dependencies explicitly documented
- No task larger than 1 day (8 hours)

---

## Example

**Input**: Technical Specification for Discount Code feature

**Output**:

```markdown
# Implementation Checklist: Discount Code Application

## Overview
- **Total Tasks**: 27 tasks
- **Total Effort**: 107 hours (13.5 person-days)
- **Estimated Duration**: 2 weeks with 2 developers
- **Priority Distribution**: P0: 15, P1: 8, P2: 4
- **Dependencies**: 18 blocking relationships

---

## Phase 1: Foundation & Setup (Day 1-2)
**Effort**: 15 hours | **Duration**: 2 days

### Setup & Configuration

- [ ] **[SETUP-001]** Initialize project repository
  - **Acceptance Criteria**:
    - Git repository created with main/develop branches
    - .gitignore configured for Node.js
    - README.md with project overview
    - MIT License added
  - **Effort**: 1 hour
  - **Priority**: P0 (blocks all)
  - **Dependencies**: None
  - **Assignee**: Tech Lead

- [ ] **[SETUP-002]** Install dependencies and configure TypeScript
  - **Acceptance Criteria**:
    - package.json with Express, TypeORM, Zod
    - tsconfig.json with strict mode
    - ESLint and Prettier configured
    - npm scripts: dev, build, test, lint
  - **Effort**: 2 hours
  - **Priority**: P0
  - **Dependencies**: SETUP-001
  - **Assignee**: Backend Dev

- [ ] **[SETUP-003]** Configure Docker and Docker Compose
  - **Acceptance Criteria**:
    - Dockerfile for Node.js app
    - docker-compose.yml with Postgres and Redis
    - Health checks configured
    - Volumes for data persistence
  - **Effort**: 3 hours
  - **Priority**: P0
  - **Dependencies**: SETUP-002
  - **Assignee**: DevOps

### Database Layer

- [ ] **[DB-001]** Create migration for discount_codes table
  - **Acceptance Criteria**:
    - Migration file: `001_create_discount_codes.sql`
    - Columns: id, code, discount_type, discount_value, min_purchase, max_uses, max_uses_per_user, valid_from, valid_until, is_active
    - PRIMARY KEY on id
    - UNIQUE index on code
    - CHECK constraints for validation
    - UP and DOWN migrations tested
  - **Effort**: 3 hours
  - **Priority**: P0
  - **Dependencies**: SETUP-003
  - **Spec Reference**: Technical Spec Section 4 (Database Schema)
  - **Assignee**: Backend Dev

- [ ] **[DB-002]** Create migration for discount_usage table
  - **Acceptance Criteria**:
    - Columns: id, discount_id, user_id, order_id, applied_at
    - Foreign keys to discount_codes, users, orders
    - Composite index on (discount_id, user_id)
  - **Effort**: 2 hours
  - **Priority**: P0
  - **Dependencies**: DB-001
  - **Assignee**: Backend Dev

- [ ] **[DB-003]** Add applied_discount_id to carts table
  - **Acceptance Criteria**:
    - Migration adds nullable FK to discount_codes
    - Migration adds discount_amount column (DECIMAL)
    - Index on applied_discount_id
  - **Effort**: 2 hours
  - **Priority**: P0
  - **Dependencies**: DB-001
  - **Assignee**: Backend Dev

- [ ] **[DB-004]** Run migrations and verify schema
  - **Acceptance Criteria**:
    - All migrations run successfully
    - Database schema matches technical spec
    - Rollback tested
  - **Effort**: 1 hour
  - **Priority**: P0
  - **Dependencies**: DB-001, DB-002, DB-003
  - **Assignee**: Backend Dev

---

## Phase 2: Core Business Logic (Day 3-7)
**Effort**: 40 hours | **Duration**: 5 days

### Domain Models

- [ ] **[MODEL-001]** Implement Discount model
  - **Acceptance Criteria**:
    - TypeScript interface matching database schema
    - Enum for discount_type (percentage, fixed)
    - Validation methods (isExpired, meetsMinimum)
  - **Effort**: 2 hours
  - **Priority**: P0
  - **Dependencies**: DB-004
  - **Assignee**: Backend Dev

- [ ] **[MODEL-002]** Implement DiscountUsage model
  - **Acceptance Criteria**:
    - TypeScript interface for usage tracking
    - Methods for calculating usage counts
  - **Effort**: 1 hour
  - **Priority**: P0
  - **Dependencies**: MODEL-001
  - **Assignee**: Backend Dev

### Repositories

- [ ] **[REPO-001]** Implement DiscountRepository
  - **Acceptance Criteria**:
    - Method: `findByCode(code: string): Promise<Discount | null>`
    - Method: `findById(id: string): Promise<Discount | null>`
    - Method: `incrementUsage(discountId, userId, orderId): Promise<void>`
    - Method: `getUserUsageCount(discountId, userId): Promise<number>`
    - Method: `getTotalUsageCount(discountId): Promise<number>`
    - Error handling for database failures
  - **Effort**: 4 hours
  - **Priority**: P0
  - **Dependencies**: MODEL-001
  - **Spec Reference**: Technical Spec Section 5.2 (DiscountRepository interface)
  - **Assignee**: Backend Dev

### Services

- [ ] **[SERVICE-001]** Implement CacheService with Redis
  - **Acceptance Criteria**:
    - Methods: get, set, delete, generateKey
    - Generic typing for type safety
    - TTL support
    - Error handling (fallback to no-cache)
  - **Effort**: 3 hours
  - **Priority**: P0
  - **Dependencies**: SETUP-003
  - **Assignee**: Backend Dev

- [ ] **[SERVICE-002]** Implement DiscountService.validateDiscount
  - **Acceptance Criteria**:
    - Check discount exists (throw INVALID_DISCOUNT_CODE if not)
    - Check not expired (throw DISCOUNT_EXPIRED if past valid_until)
    - Check min_purchase requirement (throw MINIMUM_PURCHASE_NOT_MET)
    - Check max_uses global limit
    - Check max_uses_per_user limit
    - Return validated Discount object
  - **Effort**: 4 hours
  - **Priority**: P0
  - **Dependencies**: REPO-001
  - **Spec Reference**: Technical Spec Section 6 (Error Handling)
  - **Assignee**: Backend Dev

- [ ] **[SERVICE-003]** Implement DiscountService.calculateDiscountAmount
  - **Acceptance Criteria**:
    - Percentage: `cartTotal * (discount_value / 100)`
    - Fixed: `discount_value`
    - Cap at cart total (never negative)
    - Return discount amount as number
  - **Effort**: 2 hours
  - **Priority**: P0
  - **Dependencies**: SERVICE-002
  - **Assignee**: Backend Dev

- [ ] **[SERVICE-004]** Implement DiscountService.applyDiscount
  - **Acceptance Criteria**:
    - Check cache for discount code (CacheService)
    - Validate discount (SERVICE-002)
    - Get cart total from CartService
    - Calculate discount amount (SERVICE-003)
    - Check for existing discount (throw DISCOUNT_ALREADY_APPLIED)
    - Track usage in discount_usage table
    - Update cart with applied_discount_id and discount_amount
    - Return DiscountResult object
  - **Effort**: 6 hours
  - **Priority**: P0
  - **Dependencies**: SERVICE-001, SERVICE-002, SERVICE-003
  - **Spec Reference**: Technical Spec Section 5 (DiscountService interface)
  - **Assignee**: Backend Dev

- [ ] **[SERVICE-005]** Implement DiscountService.removeDiscount
  - **Acceptance Criteria**:
    - Set cart.applied_discount_id = null
    - Set cart.discount_amount = 0
    - Recalculate cart.total
    - Return void
  - **Effort**: 2 hours
  - **Priority**: P1
  - **Dependencies**: SERVICE-004
  - **Assignee**: Backend Dev

---

## Phase 3: API Layer (Day 8-10)
**Effort**: 24 hours | **Duration**: 3 days

### Validation Schemas

- [ ] **[VALID-001]** Create Zod schema for applyDiscount request
  - **Acceptance Criteria**:
    - Schema validates: code (3-20 chars, alphanumeric), cartId (UUID)
    - Transform code to uppercase and trim
    - Export typed interface
  - **Effort**: 1 hour
  - **Priority**: P0
  - **Dependencies**: None
  - **Assignee**: Backend Dev

### Middleware

- [ ] **[MW-001]** Create rate limiting middleware
  - **Acceptance Criteria**:
    - 5 attempts per minute per user
    - Return 429 with Retry-After header
    - Custom key generator using user ID
  - **Effort**: 2 hours
  - **Priority**: P0
  - **Dependencies**: None
  - **Spec Reference**: Technical Spec Section 7 (Security)
  - **Assignee**: Backend Dev

- [ ] **[MW-002]** Create authentication middleware
  - **Acceptance Criteria**:
    - Verify JWT token from Authorization header
    - Attach user to req.user
    - Return 401 if missing/invalid token
  - **Effort**: 2 hours
  - **Priority**: P0
  - **Dependencies**: None
  - **Assignee**: Backend Dev

### Controllers

- [ ] **[API-001]** Implement POST /api/v1/cart/discount endpoint
  - **Acceptance Criteria**:
    - Validate request with VALID-001 schema
    - Call DiscountService.applyDiscount
    - Return 200 with DiscountResult
    - Return 400 for validation errors (DiscountError)
    - Return 429 for rate limit
    - Response matches OpenAPI spec exactly
  - **Effort**: 3 hours
  - **Priority**: P0
  - **Dependencies**: SERVICE-004, VALID-001, MW-001, MW-002
  - **Spec Reference**: API Spec - POST /api/v1/cart/discount
  - **Assignee**: Backend Dev

- [ ] **[API-002]** Implement DELETE /api/v1/cart/discount endpoint
  - **Acceptance Criteria**:
    - Validate cartId (UUID)
    - Call DiscountService.removeDiscount
    - Return 200 with updated cart total
    - Return 404 if cart not found
  - **Effort**: 2 hours
  - **Priority**: P1
  - **Dependencies**: SERVICE-005, MW-002
  - **Spec Reference**: API Spec - DELETE /api/v1/cart/discount
  - **Assignee**: Backend Dev

### Error Handling

- [ ] **[ERROR-001]** Implement global error handler
  - **Acceptance Criteria**:
    - Catch DiscountError, return formatted JSON
    - Catch ZodError (validation), return 400
    - Catch generic Error, return 500 with sanitized message
    - Log all errors to console/logging service
  - **Effort**: 2 hours
  - **Priority**: P0
  - **Dependencies**: API-001
  - **Assignee**: Backend Dev

---

## Phase 4: Testing (Day 11-12)
**Effort**: 20 hours | **Duration**: 2.5 days

### Unit Tests

- [ ] **[TEST-001]** Unit tests for DiscountService.validateDiscount
  - **Acceptance Criteria**:
    - Test: valid discount passes
    - Test: expired discount throws DISCOUNT_EXPIRED
    - Test: min_purchase not met throws error
    - Test: max_uses exceeded throws error
    - Test: max_uses_per_user exceeded throws error
    - Coverage: 100% of method
  - **Effort**: 3 hours
  - **Priority**: P1
  - **Dependencies**: SERVICE-002
  - **Assignee**: Backend Dev

- [ ] **[TEST-002]** Unit tests for DiscountService.calculateDiscountAmount
  - **Acceptance Criteria**:
    - Test: percentage calculation correct
    - Test: fixed amount correct
    - Test: discount capped at cart total (not negative)
    - Test: 100% discount works
  - **Effort**: 2 hours
  - **Priority**: P1
  - **Dependencies**: SERVICE-003
  - **Assignee**: Backend Dev

- [ ] **[TEST-003]** Unit tests for DiscountService.applyDiscount
  - **Acceptance Criteria**:
    - Mock dependencies (CacheService, CartService, DiscountRepository)
    - Test: successful discount application
    - Test: cache hit vs cache miss
    - Test: throws if discount already applied
  - **Effort**: 4 hours
  - **Priority**: P1
  - **Dependencies**: SERVICE-004
  - **Assignee**: Backend Dev

### Integration Tests

- [ ] **[TEST-004]** Integration test for POST /api/v1/cart/discount
  - **Acceptance Criteria**:
    - Test: valid discount code applied successfully (200)
    - Test: invalid code returns 400 with error
    - Test: expired code returns 400 with error
    - Test: min_purchase not met returns 400
    - Test: rate limit returns 429 after 5 attempts
    - Use test database with migrations
  - **Effort**: 5 hours
  - **Priority**: P0
  - **Dependencies**: API-001
  - **Spec Reference**: Functional Spec - All acceptance criteria
  - **Assignee**: QA/Backend Dev

- [ ] **[TEST-005]** Edge case tests
  - **Acceptance Criteria**:
    - Test: discount > cart total (final = $0)
    - Test: case-insensitive code entry
    - Test: whitespace trimming
    - Test: cart modified after discount (auto-remove)
  - **Effort**: 3 hours
  - **Priority**: P1
  - **Dependencies**: API-001
  - **Assignee**: QA/Backend Dev

### Test Coverage

- [ ] **[TEST-006]** Verify test coverage > 80%
  - **Acceptance Criteria**:
    - Run coverage report (npm run test:coverage)
    - Verify >80% line coverage
    - Verify >80% branch coverage
    - Fix any gaps
  - **Effort**: 2 hours
  - **Priority**: P1
  - **Dependencies**: TEST-001, TEST-002, TEST-003, TEST-004, TEST-005
  - **Assignee**: Tech Lead

---

## Phase 5: Deployment & Monitoring (Day 13)
**Effort**: 8 hours | **Duration**: 1 day

### Containerization

- [ ] **[DEPLOY-001]** Create production Dockerfile
  - **Acceptance Criteria**:
    - Multi-stage build (builder + runtime)
    - Non-root user
    - Health check endpoint
    - Image size < 200MB
  - **Effort**: 2 hours
  - **Priority**: P1
  - **Dependencies**: All tests passing
  - **Assignee**: DevOps

### CI/CD

- [ ] **[DEPLOY-002]** Configure GitHub Actions workflow
  - **Acceptance Criteria**:
    - Workflow: lint → test → build → deploy to staging
    - Run on push to main/develop
    - Deploy to production on manual approval
  - **Effort**: 3 hours
  - **Priority**: P1
  - **Dependencies**: DEPLOY-001
  - **Assignee**: DevOps

### Monitoring

- [ ] **[DEPLOY-003]** Add logging and monitoring
  - **Acceptance Criteria**:
    - Log all discount applications (Winston)
    - Log all errors with stack traces
    - Add health check endpoint (GET /health)
    - Document observability in README
  - **Effort**: 2 hours
  - **Priority**: P2
  - **Dependencies**: DEPLOY-002
  - **Assignee**: Backend Dev

---

## Milestones

### ✅ Milestone 1: Database Ready (End of Day 2)
**Criteria**:
- [ ] All migrations created and tested
- [ ] Database schema verified
- [ ] Rollback tested

**Status**: Not Started
**Blocked by**: None

---

### ✅ Milestone 2: Business Logic Complete (End of Day 7)
**Criteria**:
- [ ] All models implemented
- [ ] All repositories working
- [ ] All services implemented
- [ ] Unit tests passing

**Status**: Not Started
**Blocked by**: Milestone 1

---

### ✅ Milestone 3: API Functional (End of Day 10)
**Criteria**:
- [ ] All endpoints implemented
- [ ] Integration tests passing
- [ ] OpenAPI spec validated against responses

**Status**: Not Started
**Blocked by**: Milestone 2

---

### ✅ Milestone 4: Production Ready (End of Day 13)
**Criteria**:
- [ ] All tests passing (unit + integration)
- [ ] Test coverage >80%
- [ ] Docker image built and tested
- [ ] CI/CD pipeline working
- [ ] Deployed to staging

**Status**: Not Started
**Blocked by**: Milestone 3

---

## Effort Summary

| Phase | Tasks | Hours | Days (1 dev) | Days (2 devs) |
|-------|-------|-------|--------------|---------------|
| Phase 1: Foundation | 7 | 15 | 2 | 1.5 |
| Phase 2: Core Logic | 10 | 40 | 5 | 3 |
| Phase 3: API Layer | 6 | 24 | 3 | 2 |
| Phase 4: Testing | 6 | 20 | 2.5 | 1.5 |
| Phase 5: Deployment | 3 | 8 | 1 | 0.5 |
| **TOTAL** | **32** | **107** | **13.5** | **8.5** |

**With 20% Buffer**: 128 hours = **16 days (1 dev)** or **10 days (2 devs)**

---

## Dependencies Graph

```
SETUP-001 → SETUP-002 → SETUP-003 → DB-001 → DB-002
                                   ↓         ↓
                                DB-003 → DB-004
                                           ↓
                                      MODEL-001 → MODEL-002
                                           ↓
                                      REPO-001
                                           ↓
               SERVICE-001 → SERVICE-002 → SERVICE-003 → SERVICE-004 → SERVICE-005
                                                             ↓
                      VALID-001, MW-001, MW-002 → API-001 → API-002
                                                       ↓
                                                  ERROR-001
                                                       ↓
                          TEST-001, TEST-002, TEST-003, TEST-004, TEST-005
                                                       ↓
                                                  TEST-006
                                                       ↓
                                          DEPLOY-001 → DEPLOY-002 → DEPLOY-003
```

**Critical Path**:
SETUP-001 → SETUP-002 → SETUP-003 → DB-001 → MODEL-001 → REPO-001 → SERVICE-002 → SERVICE-003 → SERVICE-004 → API-001 → TEST-004

**Critical Path Duration**: 35 hours (4.5 days with 1 developer)

---

## Risk Assessment

| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|------------|
| Redis cache unavailability | Low | Medium | Fallback to direct DB queries (degraded performance) |
| Database migration issues | Medium | High | Test migrations in staging first, have rollback ready |
| Rate limiting too strict | Medium | Low | Make rate limit configurable via env var |
| Scope creep (product exclusions) | High | High | Document as Phase 2 feature, defer to next sprint |
| Key developer unavailable | Low | High | Pair programming, document decisions in code comments |

---

## Notes
- **Code Review**: All PRs require 1 approval before merge
- **Testing**: Run `npm test` before pushing
- **Standup**: Daily at 10am, report progress on checklist
- **Blockers**: Escalate immediately if blocked >4 hours
- **Documentation**: Update README as features complete
```

---

## Usage Notes

- **When to use**: After technical spec approved, before coding starts
- **When NOT to use**: For exploratory or research tasks
- **Best practice**: Review checklist daily, update as tasks discovered
- **Iteration**: Refine estimates after first sprint, adjust for next features

---

## Quality Checklist

- [ ] All tasks actionable and specific (no vague "implement X")
- [ ] Acceptance criteria clear and verifiable
- [ ] Effort estimates realistic (validate with team)
- [ ] Dependencies explicitly documented
- [ ] No task larger than 1 day (8 hours)
- [ ] Milestones defined with clear criteria
- [ ] Critical path identified
- [ ] Risk assessment included
- [ ] Total effort calculated with buffer
- [ ] Spec references provided for traceability
