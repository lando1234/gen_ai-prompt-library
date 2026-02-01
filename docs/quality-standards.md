# Quality Standards and Review Rubric

This document defines the quality bar for prompts in this library. Every prompt is evaluated against these criteria before being cataloged.

---

## Core Principles

1. **Explicit over implicit**: No assumptions, no "you know what I mean"
2. **Reusable over specific**: Works across contexts, not just one scenario
3. **Robust over optimistic**: Handles edge cases and failure modes
4. **Clear over clever**: Understandable beats impressive

---

## Quality Dimensions

### 1. Clarity (0-5)

**What we evaluate:**
- Are instructions unambiguous?
- Can someone unfamiliar with the domain understand and use this?
- Are technical terms defined or contextually clear?
- Is the prompt structure logical and easy to follow?

**Common failures:**
- Vague verbs: "process the data" (process how?)
- Undefined terms: "use standard practices" (whose standard?)
- Implicit knowledge: assumes user knows framework/domain
- Buried requirements: critical info hidden in long paragraphs

**5/5 example:**
```
Generate a Python function that:
1. Takes a list of integers as input
2. Returns the sum of all even numbers
3. Raises ValueError if the list is empty
4. Uses type hints and includes a docstring
```

**2/5 example:**
```
Write a function to handle the numbers properly.
```

---

### 2. Completeness (0-5)

**What we evaluate:**
- Are all required inputs listed?
- Is the output format fully specified?
- Are success criteria defined?
- Are edge cases addressed?

**Common failures:**
- Missing inputs: "Use the API" (which API? What auth?)
- Unspecified outputs: "Return the results" (in what format?)
- No success criteria: How do I know if it worked?
- Ignored edge cases: What if the input is empty/null/invalid?

**5/5 example:**
```
Inputs:
- API endpoint URL (string)
- Bearer token (string)
- Query parameters (JSON object)

Outputs:
- JSON response body if status 200-299
- Error object with status code and message otherwise

Success criteria:
- Response matches expected schema
- Status code indicates success
```

**2/5 example:**
```
Inputs: The usual stuff
Outputs: Whatever the API returns
```

---

### 3. Reusability (0-5)

**What we evaluate:**
- Does it work across different contexts?
- Is it overfitted to a single use case?
- Can it be easily adapted?
- Does it make portable assumptions?

**Common failures:**
- Hardcoded values: "Connect to db.prod.company.com"
- Company-specific jargon: "Follow the Acme API style guide"
- Single-scenario focus: "For processing Q4 2024 sales data..."
- Tool lock-in: "Use our internal framework X"

**5/5 characteristics:**
- Parameterized, not hardcoded
- Generic patterns, not company-specific
- Adaptable across domains
- Tool-agnostic where possible

**Trade-off to consider:**
- Some domain specificity is acceptable if it significantly improves quality
- Mark these as `specialized/` category and be explicit about constraints

---

### 4. Production Readiness (0-5)

**What we evaluate:**
- Has it been tested in real usage?
- Does it handle failure gracefully?
- Are error modes considered?
- Is it maintainable over time?

**Common failures:**
- "Happy path only": Assumes everything works perfectly
- No error handling: What if the API is down?
- No validation: Trusts all inputs blindly
- Brittle dependencies: Breaks if one thing changes

**5/5 characteristics:**
- Tested in production or realistic scenarios
- Handles common failure modes
- Validates inputs appropriately
- Degrades gracefully when things go wrong

**Maturity indicators:**
- `production_ready: yes` → Used in real systems, battle-tested
- `production_ready: experimental` → Promising but unvalidated

---

### 5. Structure and Format (0-5)

**What we evaluate:**
- Does it follow the template exactly?
- Is metadata complete and accurate?
- Is the prompt copy-pasteable?
- Is naming convention followed?

**Required sections:**
- Metadata block (all fields)
- Usage
- Inputs
- Outputs
- Prompt (full, uninterrupted)

**Optional but recommended:**
- Notes (for edge cases, variations, related prompts)

**Immediate rejection triggers:**
- Missing required sections
- Incomplete metadata
- Prompt split across multiple blocks
- Incorrect file naming

---

## Scoring System

Each dimension scored 0-5:

| Score | Interpretation |
|-------|----------------|
| 5 | Exemplary — ready for production use |
| 4 | Strong — minor improvements needed |
| 3 | Acceptable — works but needs refinement |
| 2 | Weak — significant gaps, not yet usable |
| 1 | Poor — fundamental issues |
| 0 | Not applicable or completely missing |

**Overall Quality Gate:**
- **Accept**: All dimensions ≥ 3, at least two dimensions = 5
- **Conditional Accept**: All dimensions ≥ 3, improvements suggested
- **Revise and Resubmit**: Any dimension < 3
- **Reject**: Multiple dimensions ≤ 2 or fundamental misalignment

---

## Review Checklist

Before cataloging a prompt, verify:

- [ ] **Clarity**: Instructions are unambiguous
- [ ] **Completeness**: All inputs and outputs explicitly defined
- [ ] **Reusability**: Not overfitted to a single scenario
- [ ] **Production Readiness**: Handles real-world edge cases
- [ ] **Format**: Follows template exactly
- [ ] **Metadata**: Complete and accurate
- [ ] **Naming**: Follows `<verb>-<object>-<qualifier>.md` convention
- [ ] **Category**: Correctly placed in taxonomy
- [ ] **Duplication**: Not redundant with existing prompts
- [ ] **Licensing**: Compatible with MIT license

---

## Common Improvement Patterns

### Problem: Vague Inputs
**Before:**
```
Inputs: The data
```

**After:**
```
Inputs:
- dataset (CSV file, UTF-8 encoded)
  - Required columns: id, timestamp, value
  - Max size: 10MB
```

---

### Problem: Unspecified Outputs
**Before:**
```
Outputs: The results
```

**After:**
```
Outputs:
- JSON object with structure:
  {
    "summary": { "count": int, "avg": float },
    "details": [ { "id": int, "processed": bool } ]
  }
- Success criteria: "processed": true for all items
```

---

### Problem: Implicit Assumptions
**Before:**
```
Use standard REST conventions.
```

**After:**
```
Follow these REST conventions:
- GET for read operations
- POST for create operations
- PUT for full updates
- PATCH for partial updates
- Return 2xx status codes for success
- Return 4xx for client errors
- Return 5xx for server errors
```

---

### Problem: Single-Use Overfitting
**Before:**
```
Generate SQL to query the users table in production database.
```

**After:**
```
Generate SQL to query a specified table:

Inputs:
- table_name: string (name of the table)
- columns: list of strings (columns to select)
- where_clause: string (optional filter condition)
```

---

## Quality Evolution

Prompts should improve over time as they're used. Indicators of a maturing prompt:

- Community adaptations and variations
- Edge cases discovered and documented
- Failure modes identified and handled
- Related prompts cross-referenced

**Encourage:**
- Opening issues for ambiguities discovered during usage
- Pull requests that strengthen existing prompts
- "Battle scars" — real production learnings added as notes

---

## Rejection is Improvement

If a prompt is rejected or marked for revision, it means:

- The concept has merit
- The execution needs refinement
- Specific improvements are required

Rejection feedback should always include:
1. **What's wrong**: Specific issues identified
2. **Why it matters**: Real-world failure scenario
3. **How to fix**: Concrete suggestions

This is a high-quality library. Rejection is not failure — it's quality control.

---

**Last Updated**: 2026-02-01
