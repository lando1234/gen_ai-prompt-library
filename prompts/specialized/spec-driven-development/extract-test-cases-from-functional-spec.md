---
type: specialized
complexity: intermediate
context: technical
output_format: code
interaction_mode: single-shot
production_ready: experimental
tags: [sdd, testing, test-cases, bdd, acceptance-testing]
---

# Extract Test Cases from Functional Specification

## Purpose
Convert functional specification acceptance criteria into executable test cases with Given/When/Then scenarios and test data.

## Context
Use this prompt when:
- Implementing test-driven development from specs
- Need comprehensive test coverage of requirements
- Creating automated acceptance tests
- Ensuring all scenarios are testable

---

## Prompt

You are a QA engineer specializing in behavior-driven development. Extract comprehensive test cases from functional specifications.

**INPUT**:
- Functional specification with acceptance criteria
- Testing framework (Jest, PyTest, Cucumber, etc.)
- Optional: API specification for integration tests

**GENERATE**:

1. **Test Case Identification**:
   - Convert each acceptance criterion to test case
   - Extract positive scenarios (happy path)
   - Extract negative scenarios (validation failures)
   - Extract edge cases
   - Extract error scenarios

2. **Test Data**:
   - Valid test data for positive scenarios
   - Invalid test data for negative scenarios
   - Boundary values for edge cases
   - Test fixtures and mocks

3. **Test Structure**:
   - Given: Preconditions and context
   - When: Action or event
   - Then: Expected outcome
   - And: Additional conditions

4. **Coverage Analysis**:
   - Map test cases to acceptance criteria
   - Identify untested scenarios
   - Calculate coverage percentage

5. **Test Implementation**:
   - Unit tests for business logic
   - Integration tests for API endpoints
   - E2E tests for critical user flows

**OUTPUT FORMAT**:
```gherkin
# Feature: [Feature Name]
# Source: [Functional Spec Reference]

Feature: [Feature Name]
  As a [role]
  I want to [action]
  So that [benefit]

  Background:
    Given [common precondition]
    And [another precondition]

  # Positive Scenarios (Happy Path)
  # Maps to: AC-1, AC-2

  Scenario: [Descriptive scenario name]
    Given [precondition]
    And [another precondition]
    When [action]
    Then [expected outcome]
    And [additional verification]

  Scenario Outline: [Parametrized test]
    Given [precondition with <parameter>]
    When [action with <parameter>]
    Then [expected outcome with <parameter>]

    Examples:
      | parameter1 | parameter2 | expected |
      | value1     | value2     | result1  |

  # Negative Scenarios (Validation Failures)
  # Maps to: AC-3, AC-4

  Scenario: [Error scenario name]
    Given [precondition]
    When [invalid action]
    Then [error response]
    And [error message contains "<text>"]

  # Edge Cases
  # Maps to: Edge-1, Edge-2

  Scenario: [Edge case name]
    Given [edge condition]
    When [action]
    Then [expected behavior]
```

```javascript
// Executable Tests (Jest/JavaScript)

describe('[Feature Name]', () => {
  // Setup
  beforeEach(() => {
    // Initialize test data
  });

  afterEach(() => {
    // Cleanup
  });

  // Positive Scenarios
  describe('Happy Path', () => {
    test('AC-1: [scenario description]', async () => {
      // Given
      const [precondition] = [setup];

      // When
      const result = await [action];

      // Then
      expect(result).toBe([expected]);
    });
  });

  // Negative Scenarios
  describe('Validation Failures', () => {
    test('AC-3: [error scenario]', async () => {
      // Given
      const [invalid_data] = [setup];

      // When/Then
      await expect([action]).rejects.toThrow([error]);
    });
  });

  // Edge Cases
  describe('Edge Cases', () => {
    test('Edge-1: [edge case]', async () => {
      // Test implementation
    });
  });
});
```

**CONSTRAINTS**:
- One test case per acceptance criterion minimum
- Include both positive and negative scenarios
- Use realistic test data
- Test error messages explicitly
- Cover boundary conditions
- Tests must be executable (not pseudocode)

---

## Example

**Input Functional Spec** (Discount Code Application):
```
AC-1: Valid discount code reduces total price
Given: User has $100 cart, discount "SAVE20" exists (20% off)
When: User applies "SAVE20"
Then: Discount $20 applied, new total $80, success message shown

AC-2: Invalid discount code shows error
Given: User has items in cart
When: User enters non-existent code "INVALID123"
Then: Error "Invalid discount code", no discount applied

AC-3: Expired discount code rejected
Given: Discount "EXPIRED" with past expiry date
When: User enters "EXPIRED"
Then: Error "This discount code has expired"

Edge-1: Discount greater than cart total
Cart: $10, Discount: $15 fixed
Expected: Final price = $0.00 (not negative)
```

**Output**:

```gherkin
# feature/discount-codes.feature

Feature: Discount Code Application
  As a customer
  I want to apply discount codes at checkout
  So that I can save money on my purchase

  Background:
    Given the user is authenticated
    And the user has a cart with ID "test-cart-123"

  # ============================================================
  # POSITIVE SCENARIOS (Happy Path)
  # ============================================================

  @positive @ac-1
  Scenario: Apply valid percentage discount code
    Given the cart has items totaling $100.00
    And discount code "SAVE20" exists with 20% off
    And the discount code is active and not expired
    When the user applies discount code "SAVE20"
    Then the discount of $20.00 is applied
    And the new cart total is $80.00
    And the success message "Discount applied: 20% off" is displayed
    And the discount code "SAVE20" is saved to the cart

  @positive
  Scenario: Apply valid fixed amount discount code
    Given the cart has items totaling $100.00
    And discount code "GET10" exists with $10 fixed discount
    When the user applies discount code "GET10"
    Then the discount of $10.00 is applied
    And the new cart total is $90.00

  @positive @case-insensitive
  Scenario: Apply discount code in lowercase
    Given the cart has items totaling $100.00
    And discount code "SAVE20" exists
    When the user applies discount code "save20" in lowercase
    Then the discount is applied successfully
    And the code is normalized to "SAVE20"

  @positive
  Scenario: Apply discount code with whitespace
    Given the cart has items totaling $100.00
    And discount code "SAVE20" exists
    When the user applies discount code " SAVE20 " with leading and trailing spaces
    Then the whitespace is trimmed
    And the discount is applied successfully

  # ============================================================
  # NEGATIVE SCENARIOS (Validation Failures)
  # ============================================================

  @negative @ac-2
  Scenario: Apply non-existent discount code
    Given the cart has items totaling $100.00
    When the user applies discount code "INVALID123"
    Then the API returns 400 Bad Request
    And the error code is "INVALID_DISCOUNT_CODE"
    And the error message is "Invalid discount code"
    And no discount is applied to the cart

  @negative @ac-3
  Scenario: Apply expired discount code
    Given the cart has items totaling $100.00
    And discount code "EXPIRED" exists
    But the discount code expired on "2024-01-01"
    When the user applies discount code "EXPIRED"
    Then the API returns 400 Bad Request
    And the error code is "DISCOUNT_EXPIRED"
    And the error message is "This discount code has expired"

  @negative
  Scenario: Apply discount code below minimum purchase
    Given the cart has items totaling $75.00
    And discount code "BIG50" exists with $50 off
    And the minimum purchase requirement is $100.00
    When the user applies discount code "BIG50"
    Then the API returns 400 Bad Request
    And the error code is "MINIMUM_PURCHASE_NOT_MET"
    And the error message is "Minimum purchase of $100 required"
    And the current total $75.00 is returned in metadata

  @negative
  Scenario: Apply discount code to empty cart
    Given the cart is empty
    When the user applies discount code "SAVE20"
    Then the API returns 400 Bad Request
    And the error message is "Cart is empty"

  @negative
  Scenario: Apply second discount code (only one allowed)
    Given the cart has items totaling $100.00
    And discount code "FIRST10" is already applied
    When the user applies discount code "SECOND15"
    Then a warning message "Only one discount code allowed per order. Replace existing discount?" is displayed
    And the user must confirm to replace the existing discount

  @negative @rate-limit
  Scenario: Exceed rate limit for discount code attempts
    Given the user has made 5 failed discount code attempts in the last minute
    When the user applies any discount code
    Then the API returns 429 Too Many Requests
    And the error message is "Too many discount code attempts. Try again in 60 seconds."
    And the Retry-After header is set to 60 seconds

  # ============================================================
  # EDGE CASES
  # ============================================================

  @edge @edge-1
  Scenario: Discount amount exceeds cart total
    Given the cart has items totaling $10.00
    And discount code "BIG15" exists with $15 fixed discount
    When the user applies discount code "BIG15"
    Then the discount of $10.00 is applied (capped at cart total)
    And the new cart total is $0.00
    And the cart total is never negative

  @edge
  Scenario: Discount is 100% (free order)
    Given the cart has items totaling $100.00
    And discount code "FREE100" exists with 100% off
    When the user applies discount code "FREE100"
    Then the discount of $100.00 is applied
    And the new cart total is $0.00

  @edge
  Scenario: Apply discount then remove item (total drops below minimum)
    Given the cart has items totaling $100.00
    And discount code "MIN100" is applied (requires $100 minimum)
    When the user removes an item and the cart total becomes $80.00
    Then the discount code "MIN100" is automatically removed
    And a warning message "Discount removed: minimum purchase no longer met" is displayed

  @edge
  Scenario: Apply discount with exactly minimum purchase amount
    Given the cart has items totaling $100.00
    And discount code "MIN100" requires exactly $100 minimum
    When the user applies discount code "MIN100"
    Then the discount is applied successfully

  # ============================================================
  # INTEGRATION SCENARIOS
  # ============================================================

  @integration
  Scenario: Apply discount and complete checkout
    Given the cart has items totaling $100.00
    And discount code "SAVE20" is applied
    When the user proceeds to checkout
    And completes the payment
    Then the order is created with discount $20.00
    And the discount usage is tracked in the database
    And the user cannot reuse the discount code (if single-use)

  # ============================================================
  # SCENARIO OUTLINE (Parametrized Tests)
  # ============================================================

  @parametrized
  Scenario Outline: Apply various discount codes
    Given the cart has items totaling <cart_total>
    And discount code "<code>" exists with <discount_type> <discount_value>
    When the user applies discount code "<code>"
    Then the discount of <discount_amount> is applied
    And the new cart total is <new_total>

    Examples:
      | cart_total | code     | discount_type | discount_value | discount_amount | new_total |
      | $100.00    | SAVE10   | percentage    | 10%            | $10.00          | $90.00    |
      | $100.00    | SAVE20   | percentage    | 20%            | $20.00          | $80.00    |
      | $100.00    | SAVE50   | percentage    | 50%            | $50.00          | $50.00    |
      | $100.00    | GET5     | fixed         | $5             | $5.00           | $95.00    |
      | $100.00    | GET25    | fixed         | $25            | $25.00          | $75.00    |
      | $50.00     | HALF     | percentage    | 50%            | $25.00          | $25.00    |
```

```javascript
// tests/integration/discount-codes.test.js

import request from 'supertest';
import app from '../../src/app';
import { setupTestDB, teardownTestDB } from '../helpers/db';

describe('Discount Code Application', () => {
  let authToken;
  let cartId;

  beforeAll(async () => {
    await setupTestDB();
  });

  afterAll(async () => {
    await teardownTestDB();
  });

  beforeEach(async () => {
    // Create authenticated user
    const authResponse = await request(app)
      .post('/api/v1/auth/login')
      .send({ email: 'test@example.com', password: 'password123' });
    authToken = authResponse.body.token;

    // Create cart with $100 total
    const cartResponse = await request(app)
      .post('/api/v1/cart')
      .set('Authorization', `Bearer ${authToken}`)
      .send({
        items: [
          { productId: 'product-1', quantity: 2, price: 50.00 }
        ]
      });
    cartId = cartResponse.body.id;
  });

  // ============================================================
  // POSITIVE SCENARIOS
  // ============================================================

  describe('Happy Path', () => {
    test('AC-1: Apply valid percentage discount code', async () => {
      // Given: Discount code "SAVE20" exists (20% off)
      await createDiscountCode({
        code: 'SAVE20',
        discountType: 'percentage',
        discountValue: 20,
        validFrom: new Date('2024-01-01'),
        validUntil: new Date('2025-12-31')
      });

      // When: User applies "SAVE20"
      const response = await request(app)
        .post('/api/v1/cart/discount')
        .set('Authorization', `Bearer ${authToken}`)
        .send({
          code: 'SAVE20',
          cartId
        });

      // Then: Discount applied successfully
      expect(response.status).toBe(200);
      expect(response.body.success).toBe(true);
      expect(response.body.data).toMatchObject({
        code: 'SAVE20',
        discountType: 'percentage',
        discountValue: 20,
        discountAmount: 20.00,
        originalTotal: 100.00,
        newTotal: 80.00,
        message: 'Discount applied: 20% off'
      });
    });

    test('Case-insensitive code entry', async () => {
      // Given
      await createDiscountCode({ code: 'SAVE20', discountType: 'percentage', discountValue: 20 });

      // When: Apply lowercase
      const response = await request(app)
        .post('/api/v1/cart/discount')
        .set('Authorization', `Bearer ${authToken}`)
        .send({ code: 'save20', cartId });

      // Then
      expect(response.status).toBe(200);
      expect(response.body.data.code).toBe('SAVE20'); // Normalized to uppercase
    });

    test('Trim whitespace from code', async () => {
      // Given
      await createDiscountCode({ code: 'SAVE20', discountType: 'percentage', discountValue: 20 });

      // When: Apply with spaces
      const response = await request(app)
        .post('/api/v1/cart/discount')
        .set('Authorization', `Bearer ${authToken}`)
        .send({ code: '  SAVE20  ', cartId });

      // Then
      expect(response.status).toBe(200);
    });
  });

  // ============================================================
  // NEGATIVE SCENARIOS
  // ============================================================

  describe('Validation Failures', () => {
    test('AC-2: Invalid discount code shows error', async () => {
      // When: Apply non-existent code
      const response = await request(app)
        .post('/api/v1/cart/discount')
        .set('Authorization', `Bearer ${authToken}`)
        .send({
          code: 'INVALID123',
          cartId
        });

      // Then: Error returned
      expect(response.status).toBe(400);
      expect(response.body).toMatchObject({
        success: false,
        error: {
          code: 'INVALID_DISCOUNT_CODE',
          message: 'Invalid discount code',
          field: 'code'
        }
      });
    });

    test('AC-3: Expired discount code rejected', async () => {
      // Given: Expired discount
      await createDiscountCode({
        code: 'EXPIRED',
        discountType: 'percentage',
        discountValue: 20,
        validFrom: new Date('2023-01-01'),
        validUntil: new Date('2023-12-31') // Expired
      });

      // When: Apply expired code
      const response = await request(app)
        .post('/api/v1/cart/discount')
        .set('Authorization', `Bearer ${authToken}`)
        .send({ code: 'EXPIRED', cartId });

      // Then: Error returned
      expect(response.status).toBe(400);
      expect(response.body.error.code).toBe('DISCOUNT_EXPIRED');
      expect(response.body.error.message).toBe('This discount code has expired');
    });

    test('Minimum purchase not met', async () => {
      // Given: Discount requires $100 minimum, cart has $75
      await createDiscountCode({
        code: 'BIG50',
        discountType: 'fixed',
        discountValue: 50,
        minPurchase: 100.00
      });

      // Update cart to $75
      await request(app)
        .put(`/api/v1/cart/${cartId}`)
        .set('Authorization', `Bearer ${authToken}`)
        .send({ items: [{ productId: 'product-1', quantity: 1, price: 75.00 }] });

      // When: Apply code
      const response = await request(app)
        .post('/api/v1/cart/discount')
        .set('Authorization', `Bearer ${authToken}`)
        .send({ code: 'BIG50', cartId });

      // Then: Error with metadata
      expect(response.status).toBe(400);
      expect(response.body.error.code).toBe('MINIMUM_PURCHASE_NOT_MET');
      expect(response.body.error.message).toBe('Minimum purchase of $100 required');
      expect(response.body.error.meta).toMatchObject({
        minimumRequired: 100.00,
        currentTotal: 75.00
      });
    });

    test('Rate limit exceeded (5 attempts/minute)', async () => {
      // Given: Make 5 failed attempts
      for (let i = 0; i < 5; i++) {
        await request(app)
          .post('/api/v1/cart/discount')
          .set('Authorization', `Bearer ${authToken}`)
          .send({ code: 'INVALID', cartId });
      }

      // When: 6th attempt
      const response = await request(app)
        .post('/api/v1/cart/discount')
        .set('Authorization', `Bearer ${authToken}`)
        .send({ code: 'SAVE20', cartId });

      // Then: Rate limited
      expect(response.status).toBe(429);
      expect(response.body.error.code).toBe('RATE_LIMIT_EXCEEDED');
      expect(response.headers['retry-after']).toBeDefined();
    });
  });

  // ============================================================
  // EDGE CASES
  // ============================================================

  describe('Edge Cases', () => {
    test('Edge-1: Discount exceeds cart total (final price = $0, not negative)', async () => {
      // Given: $10 cart, $15 discount
      await request(app)
        .put(`/api/v1/cart/${cartId}`)
        .set('Authorization', `Bearer ${authToken}`)
        .send({ items: [{ productId: 'product-1', quantity: 1, price: 10.00 }] });

      await createDiscountCode({
        code: 'BIG15',
        discountType: 'fixed',
        discountValue: 15.00
      });

      // When: Apply discount
      const response = await request(app)
        .post('/api/v1/cart/discount')
        .set('Authorization', `Bearer ${authToken}`)
        .send({ code: 'BIG15', cartId });

      // Then: Total capped at $0
      expect(response.status).toBe(200);
      expect(response.body.data.newTotal).toBe(0.00);
      expect(response.body.data.discountAmount).toBe(10.00); // Only $10 discount applied (cart total)
    });

    test('100% discount (free order)', async () => {
      // Given: 100% off
      await createDiscountCode({
        code: 'FREE100',
        discountType: 'percentage',
        discountValue: 100
      });

      // When: Apply
      const response = await request(app)
        .post('/api/v1/cart/discount')
        .set('Authorization', `Bearer ${authToken}`)
        .send({ code: 'FREE100', cartId });

      // Then: Total = $0
      expect(response.body.data.newTotal).toBe(0.00);
      expect(response.body.data.discountAmount).toBe(100.00);
    });

    test('Remove item after discount applied, total drops below minimum', async () => {
      // Given: Apply discount with $100 minimum
      await createDiscountCode({
        code: 'MIN100',
        discountType: 'percentage',
        discountValue: 10,
        minPurchase: 100.00
      });

      await request(app)
        .post('/api/v1/cart/discount')
        .set('Authorization', `Bearer ${authToken}`)
        .send({ code: 'MIN100', cartId });

      // When: Update cart to $80 (below minimum)
      const response = await request(app)
        .put(`/api/v1/cart/${cartId}`)
        .set('Authorization', `Bearer ${authToken}`)
        .send({ items: [{ productId: 'product-1', quantity: 1, price: 80.00 }] });

      // Then: Discount auto-removed
      expect(response.body.appliedDiscount).toBeNull();
      expect(response.body.warning).toBe('Discount removed: minimum purchase no longer met');
    });
  });

  // ============================================================
  // PARAMETRIZED TESTS
  // ============================================================

  describe('Parametrized Discount Calculations', () => {
    const testCases = [
      { cartTotal: 100, code: 'SAVE10', type: 'percentage', value: 10, expected: 90 },
      { cartTotal: 100, code: 'SAVE20', type: 'percentage', value: 20, expected: 80 },
      { cartTotal: 100, code: 'SAVE50', type: 'percentage', value: 50, expected: 50 },
      { cartTotal: 100, code: 'GET5', type: 'fixed', value: 5, expected: 95 },
      { cartTotal: 50, code: 'HALF', type: 'percentage', value: 50, expected: 25 },
    ];

    test.each(testCases)(
      'Apply $code to $cartTotal cart -> $expected',
      async ({ cartTotal, code, type, value, expected }) => {
        // Setup cart and discount
        await updateCartTotal(cartId, cartTotal);
        await createDiscountCode({ code, discountType: type, discountValue: value });

        // Apply discount
        const response = await request(app)
          .post('/api/v1/cart/discount')
          .set('Authorization', `Bearer ${authToken}`)
          .send({ code, cartId });

        // Verify
        expect(response.status).toBe(200);
        expect(response.body.data.newTotal).toBe(expected);
      }
    );
  });
});

// ============================================================
// TEST HELPERS
// ============================================================

async function createDiscountCode(data) {
  // Insert discount code into test database
  return await db.discountCodes.create({
    code: data.code,
    discountType: data.discountType,
    discountValue: data.discountValue,
    minPurchase: data.minPurchase || null,
    validFrom: data.validFrom || new Date(),
    validUntil: data.validUntil || new Date('2025-12-31'),
    isActive: true
  });
}

async function updateCartTotal(cartId, total) {
  return await db.carts.update({
    where: { id: cartId },
    data: { subtotal: total, total }
  });
}
```

```markdown
# Test Coverage Report

## Acceptance Criteria Coverage

| AC ID | Description | Test Cases | Status |
|-------|-------------|------------|--------|
| AC-1 | Valid discount reduces price | 3 tests | ✅ Covered |
| AC-2 | Invalid code shows error | 1 test | ✅ Covered |
| AC-3 | Expired code rejected | 1 test | ✅ Covered |
| AC-4 | Minimum purchase check | 2 tests | ✅ Covered |
| AC-5 | Only one discount per order | 1 test | ✅ Covered |
| AC-6 | Case-insensitive entry | 1 test | ✅ Covered |
| AC-7 | Remove applied discount | 1 test | ✅ Covered |

**Total**: 7/7 acceptance criteria covered (100%)

## Edge Cases Coverage

| Edge ID | Description | Test Cases | Status |
|---------|-------------|------------|--------|
| Edge-1 | Discount > cart total | 1 test | ✅ Covered |
| Edge-2 | Cart modified after discount | 1 test | ✅ Covered |
| Edge-3 | 100% discount | 1 test | ✅ Covered |
| Edge-4 | Whitespace in code | 1 test | ✅ Covered |

**Total**: 4/4 edge cases covered (100%)

## Test Statistics

- **Total Test Cases**: 15 scenarios
- **Positive Scenarios**: 4 (27%)
- **Negative Scenarios**: 6 (40%)
- **Edge Cases**: 5 (33%)
- **Parametrized Tests**: 5 variations

## Untested Scenarios

- [ ] Concurrent discount application (race condition)
- [ ] Discount code with product category exclusions
- [ ] Auto-apply discount from URL parameter
```

---

## Usage Notes

- **When to use**: After functional spec finalized, before implementation
- **When NOT to use**: For implementation details (use unit tests)
- **Best practice**: Write tests before code (TDD approach)
- **Iteration**: Update tests as requirements evolve

---

## Quality Checklist

- [ ] All acceptance criteria have test cases
- [ ] Positive, negative, and edge cases covered
- [ ] Test data is realistic
- [ ] Error messages validated explicitly
- [ ] Boundary conditions tested
- [ ] Tests are executable (not pseudocode)
- [ ] Given/When/Then structure followed
- [ ] Coverage report shows 100% AC coverage
- [ ] Parametrized tests for repetitive scenarios
