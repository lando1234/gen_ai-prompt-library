---
type: specialized
complexity: advanced
context: hybrid
output_format: structured
interaction_mode: single-shot
production_ready: experimental
tags: [sdd, drift-detection, code-review, compliance, validation]
---

# Detect Spec Drift from Implementation

## Purpose
Identify where implementation code has diverged from specifications by comparing functional requirements, technical designs, and actual code.

## Context
Use this prompt when:
- Auditing existing codebase against specs
- Detecting undocumented changes
- Ensuring implementation matches approved design
- Preparing for code reviews or compliance audits

---

## Prompt

You are a code compliance auditor specializing in specification adherence. Detect drift between specifications and implementation code.

**INPUT**:
- Functional specification
- Technical specification
- API specification (OpenAPI)
- Implementation code (source files)
- Optional: Database schema, test files

**ANALYZE**:

1. **Functional Drift**:
   - Acceptance criteria not implemented
   - Implemented features not in spec
   - Business rules violated in code
   - Error handling missing or incorrect

2. **API Drift**:
   - Endpoints in code not in API spec
   - Endpoints in spec not implemented
   - Request/response schemas mismatch
   - Status codes different from spec
   - Validation rules inconsistent

3. **Data Model Drift**:
   - Code models don't match database schema
   - Missing fields or extra fields
   - Data type mismatches
   - Relationship inconsistencies

4. **Business Logic Drift**:
   - Calculations differ from spec
   - Validation rules not enforced
   - Constraints not implemented
   - Edge cases not handled

5. **Non-Functional Drift**:
   - Performance requirements not met
   - Security measures missing
   - Logging/monitoring incomplete
   - Error handling inadequate

6. **Undocumented Changes**:
   - Code features with no spec
   - Modified logic without spec update
   - New endpoints not documented
   - Changed behavior patterns

**OUTPUT FORMAT**:
```markdown
# Specification Drift Report

## Executive Summary
- **Drift Severity**: [Critical|High|Medium|Low]
- **Total Drift Items**: [number]
- **Critical Drifts**: [number] (blocks production)
- **High Priority**: [number] (should fix)
- **Medium Priority**: [number] (technical debt)
- **Low Priority**: [number] (minor discrepancies)
- **Compliance Status**: [FAIL|WARN|PASS]

**Recommendation**: [Overall action required]

---

## 1. Functional Drift (Acceptance Criteria)

### [DRIFT-001] Missing Acceptance Criterion Implementation
**Severity**: Critical
**Type**: Feature Gap
**Spec Reference**: Functional Spec AC-5

**Specification Says**:
```
AC-5: Only one discount code per order
When user tries to apply second code "SECOND15"
Then warning message "Only one discount code allowed per order. Replace existing discount?" is displayed
And user can confirm to replace or cancel
```

**Implementation Reality**:
```javascript
// src/services/DiscountService.ts
async applyDiscount(code: string, cartId: string) {
  const discount = await this.repo.findByCode(code);
  // ❌ No check for existing discount
  await this.cartService.applyDiscount(cartId, discount);
  return { success: true };
}
```

**Evidence**:
- No validation for existing discount in `DiscountService.applyDiscount()`
- No confirmation flow in API endpoint
- Frontend doesn't prompt for replacement

**Impact**:
- Users can apply multiple discounts (business rule violation)
- Potential revenue loss
- Customer confusion

**Remediation**:
```javascript
async applyDiscount(code: string, cartId: string) {
  const cart = await this.cartService.getCart(cartId);

  // Check for existing discount
  if (cart.appliedDiscount) {
    throw new DiscountError(
      'Only one discount code allowed per order',
      'DISCOUNT_ALREADY_APPLIED',
      { existingCode: cart.appliedDiscount.code }
    );
  }

  // Apply new discount
  const discount = await this.repo.findByCode(code);
  await this.cartService.applyDiscount(cartId, discount);
}
```

---

### [DRIFT-002] Implemented Feature Not in Spec
**Severity**: Medium
**Type**: Undocumented Feature
**Spec Reference**: None (not in spec)

**Implementation Reality**:
```javascript
// src/api/routes/discount.routes.ts
router.get('/api/v1/discounts/popular', async (req, res) => {
  // Returns top 10 most used discount codes
  const popular = await discountService.getMostPopular(10);
  res.json(popular);
});
```

**Evidence**:
- `GET /api/v1/discounts/popular` endpoint exists in code
- Not documented in functional spec
- Not in API spec (OpenAPI)
- No acceptance criteria

**Impact**:
- Undocumented API contract
- Frontend teams unaware of endpoint
- No test coverage requirement
- Potential breaking changes without notice

**Remediation**:
- **Option 1**: Remove endpoint if not needed
- **Option 2**: Document in functional and API specs, add tests

---

## 2. API Drift

### [DRIFT-003] Response Schema Mismatch
**Severity**: High
**Type**: Contract Violation
**Spec Reference**: API Spec - POST /api/v1/cart/discount

**API Specification Says**:
```yaml
responses:
  '200':
    content:
      application/json:
        schema:
          properties:
            discountAmount:
              type: number
              description: Amount deducted
            newTotal:
              type: number
              description: Final price after discount
```

**Implementation Reality**:
```javascript
// src/controllers/DiscountController.ts
return res.json({
  success: true,
  discount: result.discountAmount,  // ❌ Wrong field name
  total: result.newTotal,           // ❌ Wrong field name
  originalTotal: result.originalTotal  // ✅ Not in spec (extra field)
});
```

**Evidence**:
- Field names don't match spec: `discount` vs `discountAmount`
- Field names don't match spec: `total` vs `newTotal`
- Extra field `originalTotal` not documented

**Impact**:
- Breaking change for API consumers
- Frontend code expects `discountAmount`, gets `discount`
- OpenAPI validation fails

**Remediation**:
```javascript
return res.json({
  success: true,
  discountAmount: result.discountAmount,  // ✅ Match spec
  newTotal: result.newTotal,              // ✅ Match spec
  // Remove originalTotal or add to API spec
});
```

---

### [DRIFT-004] Missing Status Code
**Severity**: High
**Type**: Error Handling Gap
**Spec Reference**: API Spec - POST /api/v1/cart/discount

**API Specification Says**:
```yaml
responses:
  '409':
    description: User with this email already exists
```

**Implementation Reality**:
```javascript
// src/controllers/DiscountController.ts
if (existingDiscount) {
  return res.status(400).json({  // ❌ Returns 400, spec says 409
    error: { code: 'DISCOUNT_ALREADY_APPLIED', message: '...' }
  });
}
```

**Evidence**:
- Spec defines 409 Conflict for "already applied" scenario
- Code returns 400 Bad Request instead

**Impact**:
- API consumers relying on 409 won't catch this case
- Inconsistent HTTP semantics

**Remediation**:
```javascript
return res.status(409).json({  // ✅ Use 409 Conflict
  error: { code: 'DISCOUNT_ALREADY_APPLIED', message: '...' }
});
```

---

## 3. Data Model Drift

### [DRIFT-005] Missing Database Field
**Severity**: Critical
**Type**: Schema Mismatch
**Spec Reference**: Database Schema - discount_codes table

**Database Schema Says**:
```sql
CREATE TABLE discount_codes (
  id UUID PRIMARY KEY,
  code VARCHAR(20) NOT NULL,
  max_uses_per_user INTEGER DEFAULT 1,  -- Specified in schema
  ...
);
```

**Implementation Reality**:
```typescript
// src/models/Discount.ts
interface Discount {
  id: string;
  code: string;
  // ❌ Missing max_uses_per_user field
  maxUses: number;  // Only global max_uses
}
```

**Evidence**:
- Database has `max_uses_per_user` column
- TypeScript model doesn't include this field
- Business logic can't enforce per-user limits

**Impact**:
- Cannot implement "single-use per user" feature
- Users can reuse discount codes indefinitely
- Revenue loss

**Remediation**:
```typescript
interface Discount {
  id: string;
  code: string;
  maxUses: number;
  maxUsesPerUser: number;  // ✅ Add missing field
}
```

---

### [DRIFT-006] Extra Field Not in Schema
**Severity**: Low
**Type**: Unused Field
**Spec Reference**: Database Schema

**Implementation Reality**:
```typescript
// src/models/Discount.ts
interface Discount {
  id: string;
  code: string;
  description: string;  // ❌ Not in database schema
}
```

**Evidence**:
- Model has `description` field
- Database schema has no `description` column

**Impact**:
- Runtime error when saving discount (column doesn't exist)
- Or field silently ignored (depending on ORM)

**Remediation**:
- **Option 1**: Remove `description` from model
- **Option 2**: Add `description` column to database schema

---

## 4. Business Logic Drift

### [DRIFT-007] Calculation Differs from Spec
**Severity**: Critical
**Type**: Logic Error
**Spec Reference**: Functional Spec BR-4

**Specification Says**:
```
BR-4: Discounts apply before taxes and shipping
Calculation order: Subtotal - Discount + Tax + Shipping = Total
```

**Implementation Reality**:
```javascript
// src/services/CartService.ts
calculateTotal(cart) {
  const subtotal = cart.items.reduce((sum, item) => sum + item.price, 0);
  const tax = subtotal * 0.1;
  const shipping = 10;
  const total = subtotal + tax + shipping;

  // ❌ Discount applied AFTER tax and shipping
  if (cart.discount) {
    total -= cart.discount.amount;
  }

  return total;
}
```

**Evidence**:
- Spec: Discount before tax
- Code: Discount after tax and shipping
- Different total amounts

**Impact**:
- Incorrect pricing
- Customer confusion
- Financial discrepancies

**Remediation**:
```javascript
calculateTotal(cart) {
  let subtotal = cart.items.reduce((sum, item) => sum + item.price, 0);

  // ✅ Apply discount before tax and shipping
  if (cart.discount) {
    subtotal -= cart.discount.amount;
  }

  const tax = subtotal * 0.1;
  const shipping = 10;
  return subtotal + tax + shipping;
}
```

---

### [DRIFT-008] Missing Validation Rule
**Severity**: High
**Type**: Business Rule Not Enforced
**Spec Reference**: Functional Spec BR-3

**Specification Says**:
```
BR-3: Discount cannot reduce total below $0
If discount > cart total, final price = $0 (not negative)
```

**Implementation Reality**:
```javascript
// src/services/DiscountService.ts
applyDiscount(cartTotal, discount) {
  if (discount.type === 'percentage') {
    return cartTotal * (discount.value / 100);
  } else {
    return discount.value;  // ❌ No check for negative total
  }
}
```

**Evidence**:
- No validation for `cartTotal - discount < 0`
- Can return negative total

**Impact**:
- Business rule violation
- Negative order totals possible
- Payment processing errors

**Remediation**:
```javascript
applyDiscount(cartTotal, discount) {
  let discountAmount;
  if (discount.type === 'percentage') {
    discountAmount = cartTotal * (discount.value / 100);
  } else {
    discountAmount = discount.value;
  }

  // ✅ Cap discount at cart total (never negative)
  return Math.min(discountAmount, cartTotal);
}
```

---

## 5. Non-Functional Drift

### [DRIFT-009] Missing Performance Optimization
**Severity**: Medium
**Type**: Performance Requirement Not Met
**Spec Reference**: Technical Spec Section 7

**Specification Says**:
```
Performance: Discount validation < 500ms
Strategy: Redis caching with 5-minute TTL
```

**Implementation Reality**:
```javascript
// src/services/DiscountService.ts
async validateDiscount(code) {
  // ❌ No caching, hits database every time
  const discount = await this.repo.findByCode(code);
  return this.checkValidity(discount);
}
```

**Evidence**:
- No Redis caching implemented
- Every request queries database
- Latency: ~150ms (within limit, but no caching safety net)

**Impact**:
- Performance degrades under load
- Database bottleneck
- No resilience if DB slows

**Remediation**:
```javascript
async validateDiscount(code) {
  // ✅ Check cache first
  let discount = await this.cache.get(`discount:${code}`);

  if (!discount) {
    discount = await this.repo.findByCode(code);
    await this.cache.set(`discount:${code}`, discount, 300); // 5 min TTL
  }

  return this.checkValidity(discount);
}
```

---

### [DRIFT-010] Missing Security Measure
**Severity**: Critical
**Type**: Security Requirement Not Implemented
**Spec Reference**: Technical Spec Section 7

**Specification Says**:
```
Security: Rate limit 5 attempts/minute per user (prevent brute-force)
```

**Implementation Reality**:
```javascript
// src/api/routes/discount.routes.ts
router.post('/api/v1/cart/discount', async (req, res) => {
  // ❌ No rate limiting middleware
  const result = await discountService.applyDiscount(req.body.code, req.body.cartId);
  res.json(result);
});
```

**Evidence**:
- No rate limiting on discount endpoint
- Brute-force attack possible

**Impact**:
- Security vulnerability
- Attackers can guess discount codes
- Potential abuse

**Remediation**:
```javascript
import rateLimit from 'express-rate-limit';

const discountLimiter = rateLimit({
  windowMs: 60 * 1000,  // 1 minute
  max: 5,               // 5 requests
  keyGenerator: (req) => req.user.id  // Per-user limit
});

router.post('/api/v1/cart/discount', discountLimiter, async (req, res) => {
  // ...
});
```

---

## 6. Test Coverage Drift

### [DRIFT-011] Missing Test Case
**Severity**: High
**Type**: Test Gap
**Spec Reference**: Functional Spec AC-7

**Specification Says**:
```
AC-7: Remove applied discount code
Given discount code is applied
When user clicks "Remove"
Then discount is removed and total recalculates
```

**Implementation Reality**:
```javascript
// tests/discount.test.js
// ❌ No test for removing discount
describe('Discount Service', () => {
  test('apply valid discount', ...);
  test('reject invalid discount', ...);
  // Missing: test('remove applied discount', ...);
});
```

**Evidence**:
- Test file has 12 tests
- Functional spec has 7 acceptance criteria
- AC-7 not tested

**Impact**:
- No automated verification of removal feature
- Regression risk

**Remediation**:
```javascript
test('AC-7: Remove applied discount', async () => {
  // Given: Discount applied
  const cart = await cartService.applyDiscount('SAVE20', cartId);
  expect(cart.appliedDiscount).toBeDefined();

  // When: Remove discount
  await cartService.removeDiscount(cartId);

  // Then: Discount removed, total recalculated
  const updatedCart = await cartService.getCart(cartId);
  expect(updatedCart.appliedDiscount).toBeNull();
  expect(updatedCart.total).toBe(updatedCart.subtotal);
});
```

---

## Drift Analysis Summary

### Drift by Category
| Category | Critical | High | Medium | Low | Total |
|----------|----------|------|--------|-----|-------|
| Functional | 1 | 0 | 1 | 0 | 2 |
| API | 0 | 2 | 0 | 0 | 2 |
| Data Model | 1 | 0 | 0 | 1 | 2 |
| Business Logic | 1 | 1 | 0 | 0 | 2 |
| Non-Functional | 1 | 0 | 1 | 0 | 2 |
| Testing | 0 | 1 | 0 | 0 | 1 |
| **TOTAL** | **4** | **4** | **2** | **1** | **11** |

### Drift by Type
- **Feature Gaps**: 2 (features in spec, not in code)
- **Undocumented Features**: 1 (features in code, not in spec)
- **Contract Violations**: 3 (API/schema mismatches)
- **Logic Errors**: 2 (incorrect implementation)
- **Security Gaps**: 1 (missing security measures)
- **Performance Issues**: 1 (missing optimizations)
- **Test Gaps**: 1 (untested scenarios)

### Compliance Status: ❌ FAIL

**Critical Issues Block Production**: 4 items must be fixed before deployment

---

## Remediation Plan

### Phase 1: Critical Fixes (Block Production) - Week 1
- [ ] **DRIFT-001**: Implement single discount validation
- [ ] **DRIFT-005**: Add `max_uses_per_user` field to model
- [ ] **DRIFT-007**: Fix calculation order (discount before tax)
- [ ] **DRIFT-010**: Add rate limiting middleware

**Estimated Effort**: 3 person-days

### Phase 2: High Priority (Should Fix) - Week 2
- [ ] **DRIFT-003**: Fix API response field names
- [ ] **DRIFT-004**: Use correct HTTP status codes
- [ ] **DRIFT-008**: Add negative total validation
- [ ] **DRIFT-011**: Add missing test case

**Estimated Effort**: 2 person-days

### Phase 3: Medium Priority (Technical Debt) - Week 3
- [ ] **DRIFT-002**: Document popular discounts endpoint or remove
- [ ] **DRIFT-009**: Implement Redis caching

**Estimated Effort**: 1 person-day

### Phase 4: Low Priority (Minor) - Backlog
- [ ] **DRIFT-006**: Remove unused `description` field

**Estimated Effort**: 0.5 person-days

---

## Prevention Recommendations

1. **Specification Reviews**: Require spec approval before coding
2. **Code Reviews**: Check implementation against spec during PR review
3. **Automated Validation**: Run OpenAPI validation in CI/CD
4. **Regular Audits**: Quarterly spec-to-code drift analysis
5. **Change Management**: Update specs whenever implementation changes
6. **Test Coverage**: Require 100% acceptance criteria coverage

---

## Appendix: Scan Methodology

### Files Analyzed
- Functional Spec: `docs/specs/functional/discount-codes.md`
- Technical Spec: `docs/specs/technical/discount-service.md`
- API Spec: `api/openapi.yaml`
- Database Schema: `migrations/001_discount_codes.sql`
- Implementation:
  - `src/services/DiscountService.ts`
  - `src/controllers/DiscountController.ts`
  - `src/models/Discount.ts`
  - `src/api/routes/discount.routes.ts`
- Tests: `tests/discount.test.js`

### Analysis Techniques
1. **Text Comparison**: Acceptance criteria vs code features
2. **Schema Validation**: OpenAPI schema vs actual responses
3. **Static Analysis**: TypeScript interfaces vs database schema
4. **Test Mapping**: Test cases vs acceptance criteria
5. **Manual Code Review**: Business logic vs spec requirements
```

---

## Usage Notes

- **When to use**: After implementation complete, before production deployment
- **When NOT to use**: During active development (expect drift)
- **Best practice**: Run drift analysis at every major milestone
- **Iteration**: Fix critical issues, re-scan, repeat until compliant

---

## Quality Checklist

- [ ] All acceptance criteria checked against implementation
- [ ] API contract validated (OpenAPI spec vs responses)
- [ ] Database schema aligned with models
- [ ] Business logic matches spec calculations
- [ ] Non-functional requirements verified (performance, security)
- [ ] Test coverage validated against acceptance criteria
- [ ] Undocumented features identified
- [ ] Severity assigned to each drift item
- [ ] Remediation plan with effort estimates
- [ ] Evidence provided for each drift (code snippets, spec references)
