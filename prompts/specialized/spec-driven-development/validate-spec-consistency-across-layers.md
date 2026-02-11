---
type: specialized
complexity: advanced
context: hybrid
output_format: structured
interaction_mode: single-shot
production_ready: experimental
tags: [sdd, validation, consistency, quality-assurance, cross-layer]
---

# Validate Spec Consistency Across Layers

## Purpose
Detect inconsistencies, gaps, and conflicts between functional specifications, technical specifications, API contracts, and database schemas.

## Context
Use this prompt when:
- Reviewing multi-layer specifications before implementation
- Detecting drift between different specification documents
- Ensuring alignment across functional, technical, and data layers
- Quality assurance before development starts

---

## Prompt

You are a specification validator specializing in cross-layer consistency analysis. Detect inconsistencies, gaps, and conflicts between specification layers.

**INPUT**:
- Functional specification document
- Technical specification document
- API specification (OpenAPI)
- Database schema (SQL DDL)
- Optional: Implementation code for drift detection

**ANALYZE**:

1. **Entity Consistency**:
   - Same entities defined across layers?
   - Field names consistent (camelCase in API, snake_case in DB)?
   - Data types compatible across layers?
   - Required fields consistent?

2. **Business Rules Alignment**:
   - Validation rules in functional spec match API schema?
   - Database constraints enforce functional requirements?
   - Technical spec implements all acceptance criteria?

3. **API-Database Mapping**:
   - API endpoints reference existing database tables?
   - Request/response schemas match database fields?
   - Foreign key relationships reflected in API?
   - API pagination matches database capabilities?

4. **Completeness Checks**:
   - All functional requirements have technical implementation?
   - All API endpoints have database backing?
   - All database tables exposed via API (if intended)?
   - Error scenarios covered in all layers?

5. **Constraint Validation**:
   - API validation rules match database CHECK constraints?
   - Functional min/max values match technical limits?
   - Enum values consistent across layers?

6. **Performance Alignment**:
   - Functional performance requirements achievable with schema design?
   - Database indexes support API query patterns?
   - Pagination/filtering strategies consistent?

**OUTPUT FORMAT**:
```markdown
# Specification Consistency Report

## Summary
- **Total Issues**: [number]
- **Critical**: [number] (blocking issues)
- **Major**: [number] (should fix before implementation)
- **Minor**: [number] (nice to have)
- **Warnings**: [number] (potential concerns)

---

## 1. Entity Consistency Issues

### Critical Issues

#### [ENTITY-001] Missing Entity in Database Schema
**Severity**: Critical
**Layer**: Database
**Description**: Entity `Discount` defined in functional and technical specs but missing in database schema.

**Evidence**:
- Functional Spec (Section 4): "The system shall support discount codes..."
- Technical Spec (Section 4): "discount_codes table with fields..."
- Database Schema: ❌ No `discount_codes` table found

**Impact**: Cannot implement discount feature without database table.

**Recommendation**: Add `discount_codes` table to schema migration.

---

### Major Issues

#### [ENTITY-002] Field Name Mismatch
**Severity**: Major
**Layer**: API ↔ Database
**Description**: Field naming inconsistent between API and database.

**Evidence**:
- API Schema: `discountAmount` (camelCase)
- Database: `discount_value` (snake_case with different name)

**Impact**: Requires mapping layer, increases complexity.

**Recommendation**: Standardize on:
- API: camelCase (`discountAmount`)
- Database: snake_case (`discount_amount`)

---

### Minor Issues

#### [ENTITY-003] Optional Field Inconsistency
**Severity**: Minor
**Layer**: API ↔ Functional
**Description**: Field `phoneNumber` optional in API but not mentioned in functional spec.

**Evidence**:
- Functional Spec: No mention of phone number
- API: `phoneNumber` defined as optional

**Impact**: Low - extra field doesn't break functionality.

**Recommendation**: Update functional spec to document phone number field.

---

## 2. Business Rules Alignment Issues

### Critical Issues

#### [RULE-001] Missing Validation Rule
**Severity**: Critical
**Layer**: Database
**Description**: Functional spec requires age 18-120, but database has no CHECK constraint.

**Evidence**:
- Functional Spec (AC-3): "User must be at least 18 years old"
- Technical Spec: CHECK constraint mentioned
- Database Schema: ❌ No CHECK constraint on `age` column

**Impact**: Invalid data can be inserted directly into database.

**Recommendation**: Add CHECK constraint:
```sql
ALTER TABLE users ADD CONSTRAINT users_age_range CHECK (age BETWEEN 18 AND 120);
```

---

#### [RULE-002] Validation Discrepancy
**Severity**: Critical
**Layer**: API ↔ Functional
**Description**: Discount minimum purchase amounts differ.

**Evidence**:
- Functional Spec (BR-4): "Minimum purchase $100 for discount BIG50"
- API Schema: `minPurchase` allows any positive number
- Database: `min_purchase DECIMAL(10,2)` no constraint

**Impact**: Different minimum could be applied than specified.

**Recommendation**: Update API and database to enforce $100 minimum for BIG50.

---

## 3. API-Database Mapping Issues

### Major Issues

#### [MAP-001] Missing Foreign Key in API
**Severity**: Major
**Layer**: API
**Description**: Database has foreign key relationship not exposed in API.

**Evidence**:
- Database: `orders.user_id` FK → `users.id`
- API: `/orders` endpoint doesn't return user information

**Impact**: Clients must make additional API call to get user details.

**Recommendation**: Add `user` object to order response or provide `/orders?expand=user`.

---

#### [MAP-002] Endpoint Missing Database Support
**Severity**: Critical
**Layer**: Database
**Description**: API endpoint defined but database schema incomplete.

**Evidence**:
- API: `GET /users/{userId}/orders` endpoint
- Database: No index on `orders.user_id` for efficient lookup

**Impact**: Poor query performance, full table scan on orders table.

**Recommendation**: Add index:
```sql
CREATE INDEX idx_orders_user_id ON orders(user_id);
```

---

## 4. Completeness Issues

### Critical Issues

#### [COMP-001] Missing Error Handling
**Severity**: Critical
**Layer**: Technical Spec
**Description**: Functional spec defines error scenario not covered in technical spec.

**Evidence**:
- Functional Spec (Error-3): "Product exclusions - discount cannot be applied to certain products"
- Technical Spec: ❌ No implementation details for product exclusions
- API: ❌ No error code for product exclusions

**Impact**: Cannot implement complete feature.

**Recommendation**: Add to technical spec:
- Database: `discount_exclusions` table with product_id and discount_id
- API: Error code `PRODUCT_EXCLUDED`

---

### Major Issues

#### [COMP-002] Incomplete Acceptance Criteria
**Severity**: Major
**Layer**: Technical Spec
**Description**: Not all acceptance criteria have technical implementation.

**Evidence**:
- Functional Spec: 7 acceptance criteria defined
- Technical Spec: Only 5 criteria addressed

**Missing**:
- AC-6: Case-insensitive code entry
- AC-7: Remove applied discount code

**Recommendation**: Add implementation details for missing criteria.

---

## 5. Data Type Compatibility Issues

### Critical Issues

#### [TYPE-001] Incompatible Data Types
**Severity**: Critical
**Layer**: API ↔ Database
**Description**: Data type mismatch can cause precision loss.

**Evidence**:
- API: `price` defined as `number` (JSON float)
- Database: `price DECIMAL(10,2)` (exact precision)

**Impact**: Floating-point precision errors in currency calculations.

**Recommendation**:
- API: Clarify `number` is decimal with 2 decimal places
- Backend: Parse to DECIMAL before database insert

---

#### [TYPE-002] UUID Format Inconsistency
**Severity**: Major
**Layer**: API
**Description**: API defines ID as string but doesn't specify UUID format.

**Evidence**:
- Database: `id UUID PRIMARY KEY`
- API: `id: type: string` (no format specified)

**Impact**: Clients don't know expected format, validation issues.

**Recommendation**: Update API schema:
```yaml
id:
  type: string
  format: uuid
```

---

## 6. Constraint Alignment Issues

### Major Issues

#### [CONST-001] Enum Value Mismatch
**Severity**: Major
**Layer**: API ↔ Database
**Description**: Different enum values defined in different layers.

**Evidence**:
- Database: `status VARCHAR(20) CHECK (status IN ('pending', 'completed', 'cancelled'))`
- API: Enum values `['pending', 'processing', 'completed', 'cancelled']`

**Impact**: API accepts 'processing' but database rejects it.

**Recommendation**: Align enum values:
- Option 1: Add 'processing' to database
- Option 2: Remove 'processing' from API

---

#### [CONST-002] Missing Length Constraint
**Severity**: Minor
**Layer**: API
**Description**: API doesn't enforce length limit that database has.

**Evidence**:
- Database: `name VARCHAR(100)`
- API: `name: type: string` (no maxLength)

**Impact**: API could accept 200-char name, database would truncate/error.

**Recommendation**: Add to API schema:
```yaml
name:
  type: string
  maxLength: 100
```

---

## 7. Performance Alignment Issues

### Critical Issues

#### [PERF-001] Missing Index for Performance Requirement
**Severity**: Critical
**Layer**: Database
**Description**: Functional spec requires fast queries but database lacks supporting index.

**Evidence**:
- Functional Spec (NFR-1): "Discount validation < 500ms"
- Database: No index on `discount_codes.code` column

**Impact**: Full table scan on every discount lookup, O(n) complexity.

**Recommendation**: Add index:
```sql
CREATE INDEX idx_discount_codes_code ON discount_codes(code);
```

---

### Major Issues

#### [PERF-002] Pagination Strategy Mismatch
**Severity**: Major
**Layer**: API ↔ Technical
**Description**: API pagination method differs from technical implementation.

**Evidence**:
- API: Offset-based pagination (`page`, `limit`)
- Technical Spec: Cursor-based pagination recommended

**Impact**: Inconsistent implementation, potential performance issues.

**Recommendation**: Align on one strategy (recommend cursor-based for large datasets).

---

## 8. Security & Authorization Issues

### Critical Issues

#### [SEC-001] Missing Authorization Check
**Severity**: Critical
**Layer**: Technical Spec
**Description**: API allows operation but authorization not specified.

**Evidence**:
- API: `DELETE /users/{userId}`
- Technical Spec: ❌ No authorization rules defined
- Functional Spec: Not mentioned

**Impact**: Security vulnerability - users might delete other users.

**Recommendation**: Add authorization:
- Users can only delete their own account
- Admins can delete any account

---

## 9. Cross-Reference Issues

### [XREF-001] Dead Reference
**Severity**: Major
**Layer**: Technical Spec
**Description**: Technical spec references API endpoint that doesn't exist.

**Evidence**:
- Technical Spec (Section 8): "Calls `GET /api/v1/cart/items`"
- API Spec: ❌ No such endpoint defined

**Recommendation**: Either add endpoint to API or update technical spec.

---

## Recommendations Summary

### Immediate Actions (Critical Issues)
1. **[ENTITY-001]**: Add `discount_codes` table to database schema
2. **[RULE-001]**: Add age CHECK constraint to users table
3. **[RULE-002]**: Enforce minimum purchase validation
4. **[MAP-002]**: Add index on `orders.user_id`
5. **[COMP-001]**: Design product exclusions system
6. **[TYPE-001]**: Clarify decimal precision for currency
7. **[PERF-001]**: Add index on `discount_codes.code`
8. **[SEC-001]**: Define authorization rules for user deletion

### Before Implementation (Major Issues)
- Standardize field naming conventions (camelCase ↔ snake_case mapping)
- Add missing foreign key relationships to API
- Complete all acceptance criteria in technical spec
- Align enum values across layers
- Resolve pagination strategy discrepancies

### Nice to Have (Minor Issues)
- Document optional fields in functional spec
- Add length constraints to API schema
- Update cross-references in documentation

---

## Validation Checklist

✅ **Passed Checks**:
- [x] All entities have unique identifiers
- [x] Primary keys defined in all tables
- [x] API uses standard HTTP status codes
- [x] Error responses follow consistent format

❌ **Failed Checks**:
- [ ] All functional requirements have technical implementation (5/7 covered)
- [ ] All database foreign keys indexed (2/3 indexed)
- [ ] All API validation rules match database constraints (3/5 match)
- [ ] All enum values consistent across layers (1/2 consistent)
- [ ] All performance requirements achievable with current schema (needs indexes)

---

## Statistics

### Issues by Severity
- Critical: 8
- Major: 7
- Minor: 3
- Warnings: 2

### Issues by Layer
- Database: 6 issues
- API: 5 issues
- Technical Spec: 4 issues
- Functional Spec: 2 issues
- Cross-layer: 3 issues

### Coverage
- Functional Spec → Technical Spec: 71% (5/7 acceptance criteria)
- Technical Spec → API: 83% (10/12 operations)
- API → Database: 78% (7/9 endpoints have proper indexes)
- Database → API: 100% (all tables exposed)

---

## Next Steps

1. **Fix Critical Issues**: Address all 8 critical issues before proceeding
2. **Review Major Issues**: Prioritize 7 major issues, fix top 5
3. **Update Documentation**: Ensure all specs reflect agreed-upon changes
4. **Re-validate**: Run consistency check again after fixes
5. **Code Review**: Validate implementation against updated specs
```

**CONSTRAINTS**:
- Identify all inconsistencies (don't assume alignment)
- Provide evidence from actual spec content
- Categorize by severity (Critical/Major/Minor)
- Include specific recommendations with code examples
- Track cross-references between layers
- Generate actionable checklist

---

## Example

**Input**:
- Functional Spec: Discount code feature (from previous prompts)
- Technical Spec: Discount service design
- API Spec: Discount endpoints
- Database Schema: discount_codes table

**Output**: [See template above with 20 specific issues identified across 9 categories]

---

## Usage Notes

- **When to use**: After all specs written, before implementation starts
- **When NOT to use**: During early drafting (too many expected inconsistencies)
- **Best practice**: Run validation after each spec update
- **Iteration**: Fix critical issues, re-validate, repeat until clean

---

## Quality Checklist

- [ ] All critical issues identified and documented
- [ ] Evidence provided for each issue (spec references)
- [ ] Recommendations include code examples where applicable
- [ ] Issues categorized by severity and layer
- [ ] Cross-references validated (no dead links)
- [ ] Statistics provided (coverage percentages)
- [ ] Actionable next steps listed
- [ ] Validation checklist for quick assessment
- [ ] Issues prioritized (critical → major → minor)
