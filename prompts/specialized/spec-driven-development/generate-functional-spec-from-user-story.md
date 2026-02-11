---
type: specialized
complexity: intermediate
context: hybrid
output_format: structured
interaction_mode: single-shot
production_ready: experimental
tags: [sdd, functional-spec, requirements, user-stories, acceptance-criteria]
---

# Generate Functional Specification from User Story

## Purpose
Transform user stories into comprehensive functional specifications with acceptance criteria, edge cases, user flows, and validation rules.

## Context
Use this prompt when:
- Starting new feature development (Spec-Driven Development)
- Need detailed requirements before technical design
- Converting product requirements into actionable specs
- Documenting expected behavior for stakeholders

---

## Prompt

You are a business analyst specializing in Spec-Driven Development. Generate a comprehensive functional specification from a user story.

**INPUT**:
- User story (As a [role], I want [feature], so that [benefit])
- Optional: Additional context, constraints, business rules

**GENERATE**:

1. **Overview**:
   - Feature name and description
   - User persona and context
   - Business value and success metrics

2. **Acceptance Criteria**:
   - Given/When/Then scenarios
   - Positive scenarios (happy path)
   - Negative scenarios (validation failures)
   - Edge cases

3. **User Flows**:
   - Step-by-step interaction flow
   - Alternative paths
   - Error handling flows

4. **Data Requirements**:
   - Input data and validation rules
   - Output data and format
   - Data transformations

5. **Business Rules**:
   - Constraints and validations
   - Calculations and logic
   - Authorization rules

6. **Non-Functional Requirements**:
   - Performance expectations
   - Security requirements
   - Accessibility needs

**OUTPUT FORMAT**:
```markdown
# Functional Specification: [Feature Name]

## 1. Overview

**Feature**: [Name]
**User Story**: [Original user story]
**Persona**: [Target user role]
**Business Value**: [Why this matters]
**Success Metrics**: [How to measure success]

## 2. Acceptance Criteria

### AC-1: [Scenario Name]
**Given** [precondition]
**When** [action]
**Then** [expected outcome]

### AC-2: [Scenario Name]
[...]

## 3. User Flows

### Primary Flow
1. [Step 1]
2. [Step 2]
[...]

### Alternative Flows
**Alt-1: [Alternative scenario]**
[...]

### Error Flows
**Error-1: [Error condition]**
[...]

## 4. Data Requirements

### Input Data
| Field | Type | Required | Validation | Example |
|-------|------|----------|------------|---------|
| [...]

### Output Data
[...]

## 5. Business Rules

**BR-1**: [Rule description]
**BR-2**: [...]

## 6. Edge Cases

**Edge-1**: [Edge case description]
**Expected behavior**: [...]

## 7. Non-Functional Requirements

- **Performance**: [...]
- **Security**: [...]
- **Accessibility**: [...]

## 8. Dependencies

- [External systems, APIs, services]

## 9. Out of Scope

- [What is explicitly NOT included]
```

**CONSTRAINTS**:
- Acceptance criteria must be testable (no ambiguity)
- Cover positive, negative, and edge cases
- Specify validation rules explicitly
- Include error handling scenarios
- Define clear success metrics

---

## Example

**Input User Story**:
```
As a customer
I want to apply a discount code at checkout
So that I can save money on my purchase
```

**Output**:

```markdown
# Functional Specification: Discount Code Application

## 1. Overview

**Feature**: Discount Code Application at Checkout
**User Story**: As a customer, I want to apply a discount code at checkout so that I can save money on my purchase
**Persona**: Online shopper making a purchase
**Business Value**:
- Increase conversion rates through promotional campaigns
- Enable marketing team to run targeted discount campaigns
- Reduce cart abandonment by offering incentives

**Success Metrics**:
- 30% of checkouts use discount codes
- <5% discount code error rate
- Average discount redemption time <3 seconds

## 2. Acceptance Criteria

### AC-1: Valid discount code reduces total price
**Given** user has items in cart totaling $100
**And** discount code "SAVE20" exists with 20% off
**When** user enters "SAVE20" and clicks "Apply"
**Then** discount of $20 is applied
**And** new total shows $80
**And** success message "Discount applied: 20% off" is displayed

### AC-2: Invalid discount code shows error
**Given** user has items in cart
**When** user enters non-existent code "INVALID123"
**Then** error message "Invalid discount code" is displayed
**And** no discount is applied
**And** original total remains unchanged

### AC-3: Expired discount code is rejected
**Given** discount code "EXPIRED" with expiry date in the past
**When** user enters "EXPIRED"
**Then** error message "This discount code has expired" is displayed

### AC-4: Discount code with minimum purchase requirement
**Given** discount code "BIG50" requires minimum $100 purchase
**And** user cart total is $75
**When** user enters "BIG50"
**Then** error message "Minimum purchase of $100 required" is displayed

### AC-5: Only one discount code per order
**Given** discount code "FIRST10" is already applied
**When** user tries to apply second code "SECOND15"
**Then** warning message "Only one discount code allowed per order. Replace existing discount?" is displayed
**And** user can confirm to replace or cancel

### AC-6: Case-insensitive code entry
**Given** discount code "SAVE20" exists
**When** user enters "save20" (lowercase)
**Then** discount is applied successfully

### AC-7: Remove applied discount code
**Given** discount code is applied
**When** user clicks "Remove" next to applied code
**Then** discount is removed
**And** total price recalculates to original amount

## 3. User Flows

### Primary Flow: Apply Discount Code
1. User navigates to checkout page
2. User sees "Have a discount code?" input field
3. User enters discount code (e.g., "SAVE20")
4. User clicks "Apply" button
5. System validates code (exists, not expired, meets conditions)
6. System calculates discount amount
7. System updates cart total with discount applied
8. System displays success message with discount details
9. Discount summary shown in order breakdown

### Alternative Flows

**Alt-1: Replace Existing Discount**
1. User already has discount code applied
2. User enters different code
3. System prompts: "Replace current discount?"
4. If user confirms:
   - Remove old discount
   - Apply new discount
5. If user cancels:
   - Keep existing discount
   - Discard new code

**Alt-2: Auto-Apply from URL Parameter**
1. User arrives via marketing link with ?code=SUMMER25
2. System automatically validates and applies code
3. User sees success message on checkout page

### Error Flows

**Error-1: Empty Code Submission**
- User clicks "Apply" with empty input
- Inline error: "Please enter a discount code"

**Error-2: Code Already Used (Single-Use)**
- User enters code already used in previous order
- Error: "This code has already been used"

**Error-3: Product Exclusions**
- User cart contains excluded products
- Error: "This discount cannot be applied to [product name]"

## 4. Data Requirements

### Input Data

| Field | Type | Required | Validation | Example |
|-------|------|----------|------------|---------|
| discount_code | string | Yes | 3-20 alphanumeric chars, trim whitespace | "SAVE20" |
| cart_total | decimal | Yes | > 0 | 99.99 |
| user_id | string | No | UUID format | "user_123" |

### Output Data

| Field | Type | Description | Example |
|-------|------|-------------|---------|
| discount_amount | decimal | Amount deducted | 20.00 |
| discount_percentage | integer | % discount applied | 20 |
| new_total | decimal | Final price after discount | 79.99 |
| success_message | string | User feedback | "Discount applied: 20% off" |

### Data Transformations
- Discount code input: Convert to uppercase, trim whitespace
- Percentage discount: Calculate as `cart_total * (discount_percentage / 100)`
- Fixed discount: Subtract directly, but not below $0

## 5. Business Rules

**BR-1**: Discount codes are case-insensitive
- System converts all codes to uppercase before validation

**BR-2**: Only one discount code per order
- If user applies second code, must replace first (with confirmation)

**BR-3**: Discount cannot reduce total below $0
- If discount > cart total, final price = $0 (not negative)

**BR-4**: Discounts apply before taxes and shipping
- Calculation order: Subtotal - Discount + Tax + Shipping = Total

**BR-5**: Some products may be excluded from discounts
- Check product.discount_eligible flag
- Show error if cart contains only ineligible items

**BR-6**: Expired codes cannot be applied
- Check discount.expiry_date < current_date

**BR-7**: Usage limits enforced
- Single-use codes: Check user hasn't used before
- Global usage limit: Check total redemptions < max_uses

## 6. Edge Cases

**Edge-1: Discount greater than cart total**
- Cart: $10, Discount: $15 (or 200% off)
- **Expected behavior**: Final price = $0.00, not negative

**Edge-2: Code with minimum purchase, then item removed**
- Code applied with $100 cart (min $100 required)
- User removes item, cart now $80
- **Expected behavior**: Discount automatically removed, warning shown

**Edge-3: Multiple discounts in database with same code**
- Rare data integrity issue
- **Expected behavior**: Use most generous discount, log error for admin review

**Edge-4: Code entered with leading/trailing spaces**
- User enters " SAVE20 " (with spaces)
- **Expected behavior**: Trim and apply successfully

**Edge-5: Code valid but product category excluded**
- Code "ELECTRONICS20" only for electronics
- User cart has only clothing
- **Expected behavior**: Error "This code is only valid for electronics"

**Edge-6: Code application during session timeout**
- User applies code, walks away, session expires
- User returns and proceeds to payment
- **Expected behavior**: Re-validate code before final charge

## 7. Non-Functional Requirements

**Performance**:
- Discount validation: <500ms response time
- Code database lookup: Indexed for O(1) retrieval
- Handle 1000+ concurrent discount applications

**Security**:
- Prevent brute-force code guessing (rate limit: 5 attempts/min)
- Validate on server-side (never trust client)
- Log all discount applications for audit trail

**Accessibility**:
- Input field has clear label and ARIA attributes
- Error messages announced to screen readers
- Keyboard-accessible apply/remove buttons

**Usability**:
- Show real-time validation (typing indicator)
- Clear error messages (no technical jargon)
- Undo option immediately after applying

## 8. Dependencies

- **Discount Code Service**: API to validate and retrieve discount details
  - `GET /api/discounts/:code` - Fetch discount info
  - `POST /api/discounts/:code/apply` - Apply and track usage

- **Cart Service**: Current cart total and items
  - `GET /api/cart` - Retrieve cart state
  - `PUT /api/cart/discount` - Update discount applied

- **User Service**: User ID for single-use code tracking
  - `GET /api/users/:id/discount-usage` - Check if code used before

## 9. Out of Scope

- **NOT included in this spec**:
  - Discount code creation UI (admin panel)
  - Automatic discount suggestions ("You might like...")
  - Loyalty points redemption (separate feature)
  - Gift card application (different payment method)
  - Referral code tracking (separate system)

---

## Usage Notes

- **When to use**: Starting new feature development in SDD workflow
- **When NOT to use**: For trivial features with obvious requirements
- **Best practice**: Review spec with stakeholders before technical design
- **Iteration**: Refine based on technical feasibility and edge case discovery

---

## Quality Checklist

- [ ] All acceptance criteria are testable (clear pass/fail)
- [ ] Positive, negative, and edge cases covered
- [ ] User flows include alternative and error paths
- [ ] Data validation rules explicitly defined
- [ ] Business rules documented with rationale
- [ ] Non-functional requirements specified
- [ ] Dependencies identified
- [ ] Out-of-scope items clarified
- [ ] Success metrics measurable
