---
type: code-generation
complexity: intermediate
context: technical
output_format: code
interaction_mode: single-shot
production_ready: experimental
tags: [refactoring, clean-code, maintainability, code-quality]
---

# Refactor Code for Readability

## Purpose
Improve code readability and maintainability by applying clean code principles without changing functionality.

## Context
Use this prompt when:
- Code review identifies readability issues
- Onboarding new developers to codebase
- Legacy code needs modernization
- Preparing code for production

---

## Prompt

You are a code quality expert. Refactor the provided code to improve readability and maintainability while preserving functionality.

**INPUT**:
- Code to refactor
- Programming language
- Optional: Specific issues to address

**APPLY**:

1. **Naming Improvements**:
   - Clear, descriptive variable/function names
   - Avoid abbreviations unless standard (e.g., "id", "url")
   - Use consistent naming conventions (camelCase, snake_case)

2. **Function Extraction**:
   - Extract long functions into smaller, focused ones
   - Single Responsibility Principle
   - Max 20-30 lines per function

3. **Remove Code Smells**:
   - Magic numbers → Named constants
   - Deep nesting → Early returns/guard clauses
   - Long parameter lists → Objects/config
   - Duplicate code → Extract to function

4. **Improve Structure**:
   - Group related code together
   - Logical ordering (top-to-bottom flow)
   - Add whitespace for readability

5. **Documentation**:
   - Add docstrings/comments for "why", not "what"
   - Document complex algorithms
   - Remove obvious/outdated comments

**OUTPUT FORMAT**:
```
# Refactored Code: [Original Function/Class Name]

## Changes Made
1. [Change type]: [Specific improvement]
2. [Change type]: [Specific improvement]

## Before
```[language]
[original code]
```

## After
```[language]
[refactored code]
```

## Impact
- Readability: [Improvement description]
- Maintainability: [How easier to modify]
- Performance: [No change / Improved / Explanation]
```

**CONSTRAINTS**:
- NEVER change functionality or external behavior
- Keep all existing tests passing
- Maintain backward compatibility
- Don't over-engineer simple code
- Balance readability with performance

---

## Example

**Input Code**:
```python
def p(d, c, t):
    if t == 'r':
        x = d * 0.9
    elif t == 'p':
        x = d * 0.85
    else:
        x = d
    if c == 'US':
        y = x + x * 0.08
    elif c == 'CA':
        y = x + x * 0.13
    else:
        y = x
    return round(y, 2)
```

**Output**:

```python
# Refactored Code: Price Calculator

## Changes Made
1. **Naming**: Replaced cryptic variable names (p, d, c, t, x, y) with descriptive names
2. **Magic Numbers**: Extracted discount rates and tax rates to named constants
3. **Function Extraction**: Separated discount and tax logic into focused functions
4. **Documentation**: Added docstrings explaining purpose and parameters
5. **Type Hints**: Added type annotations for clarity
6. **Structure**: Improved flow with early returns and clear sections

## Before
```python
def p(d, c, t):
    if t == 'r':
        x = d * 0.9
    elif t == 'p':
        x = d * 0.85
    else:
        x = d
    if c == 'US':
        y = x + x * 0.08
    elif c == 'CA':
        y = x + x * 0.13
    else:
        y = x
    return round(y, 2)
```

## After
```python
from typing import Literal

# Named constants for magic numbers
REGULAR_DISCOUNT_RATE = 0.10  # 10% off for regular customers
PREMIUM_DISCOUNT_RATE = 0.15  # 15% off for premium customers

TAX_RATES = {
    'US': 0.08,  # 8% sales tax
    'CA': 0.13,  # 13% HST
}
DEFAULT_TAX_RATE = 0.00  # No tax for other countries

CustomerType = Literal['regular', 'premium', 'none']
CountryCode = str


def calculate_final_price(
    base_price: float,
    country_code: CountryCode,
    customer_type: CustomerType
) -> float:
    """
    Calculate final price including customer discount and country-specific tax.

    Args:
        base_price: Original price before discounts and taxes
        country_code: ISO country code (e.g., 'US', 'CA')
        customer_type: 'regular' (10% off), 'premium' (15% off), or 'none'

    Returns:
        Final price rounded to 2 decimal places

    Example:
        >>> calculate_final_price(100.0, 'US', 'premium')
        91.80  # 100 * 0.85 = 85, then 85 * 1.08 = 91.80
    """
    price_after_discount = _apply_customer_discount(base_price, customer_type)
    price_with_tax = _apply_country_tax(price_after_discount, country_code)
    return round(price_with_tax, 2)


def _apply_customer_discount(price: float, customer_type: CustomerType) -> float:
    """
    Apply discount based on customer type.

    Args:
        price: Price before discount
        customer_type: Type of customer

    Returns:
        Price after applying appropriate discount
    """
    if customer_type == 'regular':
        return price * (1 - REGULAR_DISCOUNT_RATE)
    elif customer_type == 'premium':
        return price * (1 - PREMIUM_DISCOUNT_RATE)
    else:
        return price  # No discount


def _apply_country_tax(price: float, country_code: CountryCode) -> float:
    """
    Apply tax based on country.

    Args:
        price: Price before tax
        country_code: ISO country code

    Returns:
        Price after applying country-specific tax rate
    """
    tax_rate = TAX_RATES.get(country_code, DEFAULT_TAX_RATE)
    return price * (1 + tax_rate)
```

## Impact

**Readability Improvements**:
- Function name `calculate_final_price` clearly describes purpose vs cryptic `p`
- Variable names (`base_price`, `customer_type`) are self-documenting
- Magic numbers extracted to named constants with comments
- Type hints make expected inputs/outputs obvious
- Docstring provides usage examples

**Maintainability Improvements**:
- Adding new customer types: Update constants and one function
- Adding new countries: Update TAX_RATES dictionary
- Changing discount logic: Isolated to `_apply_customer_discount`
- Each function has single responsibility, easier to test/modify

**Performance**:
- No performance impact (same operations, just reorganized)
- Constant lookups are O(1), negligible overhead

**Code Metrics**:
- Before: 1 function, 12 lines, cyclomatic complexity 5
- After: 3 functions, 35 lines (with docs), complexity 2 per function
- Lines increased due to documentation, but each unit is simpler

---

## Usage Notes

- **When to use**: Code is hard to understand or modify
- **When NOT to use**: Code is already clean and simple
- **Best practice**: Refactor incrementally, run tests after each change
- **Iteration**: Multiple refactoring passes may be needed for complex code

---

## Quality Checklist

- [ ] All names are clear and descriptive
- [ ] Magic numbers replaced with named constants
- [ ] Functions are focused (single responsibility)
- [ ] Deep nesting reduced with guard clauses
- [ ] Duplicate code extracted to reusable functions
- [ ] Documentation added where needed
- [ ] Existing tests still pass
- [ ] Functionality unchanged (no new behaviors)
