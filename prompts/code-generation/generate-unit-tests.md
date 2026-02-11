---
type: code-generation
complexity: intermediate
context: technical
output_format: code
interaction_mode: single-shot
production_ready: experimental
tags: [testing, unit-tests, jest, pytest, junit, tdd]
---

# Generate Unit Tests for Function

## Purpose
Generate comprehensive unit tests for a function covering normal cases, edge cases, error scenarios, and mocking dependencies.

## Context
Use this prompt when:
- Implementing TDD (Test-Driven Development)
- Adding tests to legacy code
- Need comprehensive test coverage quickly
- Validating function behavior across scenarios

---

## Prompt

You are a test engineer. Generate comprehensive unit tests for the provided function with full coverage of normal, edge, and error cases.

**INPUT**:
- Function code or signature
- Programming language
- Testing framework (Jest, PyTest, JUnit, etc.)
- Optional: Dependencies to mock

**GENERATE**:

1. **Test Structure**:
   - Test suite/describe block
   - Setup/teardown if needed
   - Grouped by scenario type

2. **Test Coverage**:
   - **Happy Path** (60%): Normal, expected inputs
   - **Edge Cases** (30%): Boundaries, empty, null, special values
   - **Error Cases** (10%): Invalid inputs, exceptions

3. **For Each Test**:
   - Descriptive test name
   - Arrange: Setup inputs and mocks
   - Act: Execute function
   - Assert: Verify output and side effects

4. **Mocking**:
   - Mock external dependencies
   - Mock database calls
   - Mock API requests
   - Control time-dependent behavior

5. **Assertions**:
   - Return value correctness
   - Exception types and messages
   - Side effects (state changes, calls made)
   - Performance (if relevant)

**OUTPUT FORMAT**:
```
# Unit Tests: [Function Name]

## Test Coverage Strategy
- Happy path: [X tests]
- Edge cases: [Y tests]
- Error scenarios: [Z tests]
- Total: [X+Y+Z tests]

## Test Suite

```[language]
[complete test code with all cases]
```

## Coverage Analysis
- Lines covered: [percentage]
- Branches covered: [percentage]
- Edge cases tested: [list]

## Notes
- [Assumptions made]
- [Difficult-to-test scenarios]
- [Suggestions for improvement]
```

**CONSTRAINTS**:
- Aim for 80%+ code coverage
- Tests must be independent (no shared state)
- Use descriptive test names (what is tested, not how)
- Mock all external dependencies
- Include at least 1 test per code branch
- Verify both success and failure paths

---

## Example

**Input Function**:
```python
def calculate_discount(price: float, discount_percent: float, customer_type: str) -> float:
    """
    Calculate final price after discount.

    Args:
        price: Original price (must be positive)
        discount_percent: Discount percentage 0-100
        customer_type: 'regular' or 'premium' (premium gets extra 5% off)

    Returns:
        Final price after discount

    Raises:
        ValueError: If price <= 0 or discount not in 0-100 range
    """
    if price <= 0:
        raise ValueError("Price must be positive")
    if not 0 <= discount_percent <= 100:
        raise ValueError("Discount must be between 0 and 100")

    discount_amount = price * (discount_percent / 100)
    final_price = price - discount_amount

    if customer_type == 'premium':
        final_price *= 0.95  # Extra 5% off for premium

    return round(final_price, 2)
```

**Testing Framework**: PyTest

**Output**:

```python
# Unit Tests: calculate_discount

## Test Coverage Strategy
- Happy path: 4 tests (regular customer, premium customer, various prices)
- Edge cases: 6 tests (0% discount, 100% discount, boundary values)
- Error scenarios: 3 tests (negative price, invalid discount, wrong customer type)
- Total: 13 tests

## Test Suite

```python
import pytest
from decimal import Decimal
from your_module import calculate_discount


class TestCalculateDiscount:
    """Test suite for calculate_discount function."""

    # --- HAPPY PATH TESTS ---

    def test_regular_customer_with_10_percent_discount(self):
        """Regular customer gets standard 10% discount."""
        result = calculate_discount(100.0, 10.0, 'regular')
        assert result == 90.0

    def test_premium_customer_with_10_percent_discount(self):
        """Premium customer gets 10% + extra 5% discount."""
        result = calculate_discount(100.0, 10.0, 'premium')
        # 100 - 10 = 90, then 90 * 0.95 = 85.50
        assert result == 85.50

    def test_large_price_with_small_discount(self):
        """High-value purchase with minimal discount."""
        result = calculate_discount(10000.0, 2.0, 'regular')
        assert result == 9800.0

    def test_small_price_with_large_discount(self):
        """Low-value purchase with significant discount."""
        result = calculate_discount(50.0, 80.0, 'regular')
        assert result == 10.0

    # --- EDGE CASE TESTS ---

    def test_zero_percent_discount(self):
        """No discount applied, original price returned."""
        result = calculate_discount(100.0, 0.0, 'regular')
        assert result == 100.0

    def test_hundred_percent_discount(self):
        """Full discount, price becomes zero."""
        result = calculate_discount(100.0, 100.0, 'regular')
        assert result == 0.0

    def test_premium_with_zero_discount(self):
        """Premium customer with no base discount still gets 5% off."""
        result = calculate_discount(100.0, 0.0, 'premium')
        assert result == 95.0

    def test_very_small_price(self):
        """Handle prices less than 1 dollar."""
        result = calculate_discount(0.50, 10.0, 'regular')
        assert result == 0.45

    def test_price_requiring_rounding(self):
        """Ensure proper rounding to 2 decimal places."""
        result = calculate_discount(99.99, 33.33, 'regular')
        # 99.99 * 0.6667 = 66.6600... should round to 66.66
        assert result == 66.66

    def test_unknown_customer_type_treated_as_regular(self):
        """Non-premium customer type defaults to regular behavior."""
        result = calculate_discount(100.0, 10.0, 'vip')
        assert result == 90.0  # No extra discount

    # --- ERROR SCENARIO TESTS ---

    def test_negative_price_raises_value_error(self):
        """Negative price should raise ValueError."""
        with pytest.raises(ValueError, match="Price must be positive"):
            calculate_discount(-100.0, 10.0, 'regular')

    def test_zero_price_raises_value_error(self):
        """Zero price should raise ValueError."""
        with pytest.raises(ValueError, match="Price must be positive"):
            calculate_discount(0.0, 10.0, 'regular')

    def test_discount_below_zero_raises_value_error(self):
        """Discount below 0% should raise ValueError."""
        with pytest.raises(ValueError, match="Discount must be between 0 and 100"):
            calculate_discount(100.0, -5.0, 'regular')

    def test_discount_above_hundred_raises_value_error(self):
        """Discount above 100% should raise ValueError."""
        with pytest.raises(ValueError, match="Discount must be between 0 and 100"):
            calculate_discount(100.0, 150.0, 'regular')

    # --- PARAMETRIZED TESTS (bonus for efficiency) ---

    @pytest.mark.parametrize("price,discount,expected", [
        (100.0, 10.0, 90.0),
        (200.0, 25.0, 150.0),
        (50.0, 50.0, 25.0),
    ])
    def test_various_regular_discounts(self, price, discount, expected):
        """Test multiple regular discount scenarios efficiently."""
        result = calculate_discount(price, discount, 'regular')
        assert result == expected

    @pytest.mark.parametrize("price,discount,expected", [
        (100.0, 10.0, 85.50),
        (200.0, 20.0, 152.0),
    ])
    def test_various_premium_discounts(self, price, discount, expected):
        """Test multiple premium discount scenarios efficiently."""
        result = calculate_discount(price, discount, 'premium')
        assert result == expected
```

## Coverage Analysis
- Lines covered: 100% (all lines executed by tests)
- Branches covered: 100% (all if/else paths tested)
- Edge cases tested:
  * 0% discount
  * 100% discount
  * Very small prices (<$1)
  * Rounding edge cases
  * Unknown customer types
- Error cases tested:
  * Negative price
  * Zero price
  * Discount < 0
  * Discount > 100

## Notes
- **Assumptions**: Unknown customer types default to regular (no extra discount)
- **Rounding**: Function rounds to 2 decimals; tests verify this behavior
- **Parametrized tests**: Used pytest.mark.parametrize to reduce code duplication for similar scenarios
- **Error messages**: Tests verify not just that exceptions are raised, but also their messages
- **Edge case discovery**: Tests for rounding edge case (99.99 * 33.33%) found potential precision issue
- **Suggestions**:
  * Add type hints validation if using Python 3.10+ (TypeGuard)
  * Consider logging for unusual customer_type values
  * Add integration test with actual customer database

---

## Usage Notes

- **When to use**: Writing tests for new or existing functions
- **When NOT to use**: For trivial getters/setters with no logic
- **Best practice**: Write tests before implementation (TDD) or immediately after
- **Iteration**: Add tests when bugs discovered in production

---

## Quality Checklist

- [ ] At least 80% code coverage
- [ ] All code branches tested (if/else, try/catch)
- [ ] Happy path tests cover normal usage
- [ ] Edge cases include boundaries and special values
- [ ] Error scenarios verify exception types and messages
- [ ] Tests are independent (no shared state)
- [ ] Descriptive test names explain what is tested
- [ ] External dependencies mocked appropriately
