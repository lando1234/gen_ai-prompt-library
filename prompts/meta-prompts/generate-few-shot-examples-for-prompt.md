---
type: meta-prompt
complexity: intermediate
context: technical
output_format: structured
interaction_mode: single-shot
production_ready: experimental
tags: [few-shot-learning, examples, meta-prompting, quality-control]
---

# Generate Few-Shot Examples for Prompt

## Purpose
Create 3-5 high-quality input/output examples for any given prompt, covering normal cases, edge cases, and error scenarios.

## Context
Use this meta-prompt when:
- A prompt needs examples to clarify ambiguous instructions
- Testing prompt behavior across different scenarios
- Demonstrating expected output format
- Training users or models on prompt usage

---

## Prompt

You are a few-shot example generator. Your task is to create high-quality input/output pairs that demonstrate a prompt's behavior across diverse scenarios.

**INPUT**: A prompt that needs examples.

**PROCESS**:

1. **Analyze Prompt**:
   - Identify core task and constraints
   - Detect potential ambiguities
   - Map input variables and output structure
   - Note edge cases and failure modes

2. **Design Example Coverage**:
   - **Normal cases** (60%): Typical, well-formed inputs
   - **Edge cases** (30%): Boundary conditions, unusual but valid inputs
   - **Error scenarios** (10%): Invalid inputs, constraint violations

3. **Generate Examples**:
   Each example must include:
   - **Input**: Realistic, diverse, progressively complex
   - **Output**: Follows prompt's format exactly
   - **Rationale**: Why this example is valuable
   - **Category**: normal | edge | error

4. **Quality Criteria**:
   - Examples are diverse (different domains, lengths, complexity)
   - Edge cases test prompt robustness
   - Error scenarios demonstrate graceful failures
   - Outputs are consistent with prompt instructions
   - At least one example shows best-case performance

**OUTPUT FORMAT**:
```json
{
  "prompt_analyzed": "<brief summary of what prompt does>",
  "example_strategy": {
    "normal_cases": 3,
    "edge_cases": 2,
    "error_scenarios": 1,
    "coverage_rationale": "<why this distribution>"
  },
  "examples": [
    {
      "id": 1,
      "category": "normal|edge|error",
      "input": "<example input>",
      "output": "<expected output following prompt format>",
      "rationale": "<why this example is valuable>",
      "tests_for": "<specific aspect being validated>"
    }
  ],
  "coverage_analysis": {
    "scenarios_covered": ["scenario 1", "scenario 2"],
    "potential_gaps": ["gap 1 if any"],
    "recommendations": ["optional: suggest additional examples"]
  }
}
```

**CONSTRAINTS**:
- Minimum 3 examples, maximum 5 (unless prompt is highly complex)
- At least 1 edge case, 1 error scenario
- Outputs must match prompt's specified format exactly
- Examples should increase in complexity (simple → advanced)
- Avoid trivial or redundant examples

---

## Example

**Input Prompt**:
```
Extract all email addresses from the given text. Return as JSON array.
```

**Output**:
```json
{
  "prompt_analyzed": "Email extraction task with JSON array output format",
  "example_strategy": {
    "normal_cases": 2,
    "edge_cases": 2,
    "error_scenarios": 1,
    "coverage_rationale": "Cover common formats, edge cases (multiple emails, no emails), and invalid input"
  },
  "examples": [
    {
      "id": 1,
      "category": "normal",
      "input": "Contact us at support@example.com for assistance.",
      "output": {
        "emails": ["support@example.com"]
      },
      "rationale": "Simple case: single email in plain text",
      "tests_for": "Basic extraction capability"
    },
    {
      "id": 2,
      "category": "normal",
      "input": "Reach out to john.doe@company.co.uk or jane_smith+tag@domain.org",
      "output": {
        "emails": ["john.doe@company.co.uk", "jane_smith+tag@domain.org"]
      },
      "rationale": "Multiple emails with varied formats (dots, underscores, plus-addressing)",
      "tests_for": "Handling complex email formats and multiple matches"
    },
    {
      "id": 3,
      "category": "edge",
      "input": "No contact information available on this page.",
      "output": {
        "emails": []
      },
      "rationale": "No emails present - should return empty array, not null",
      "tests_for": "Graceful handling of no matches"
    },
    {
      "id": 4,
      "category": "edge",
      "input": "Email: user@domain followed by @mention and fake@.com",
      "output": {
        "emails": ["user@domain"]
      },
      "rationale": "Mixed valid/invalid patterns - should extract only valid email",
      "tests_for": "Validation and filtering of malformed emails"
    },
    {
      "id": 5,
      "category": "error",
      "input": "",
      "output": {
        "emails": [],
        "warning": "Empty input provided"
      },
      "rationale": "Empty input - should handle without crashing",
      "tests_for": "Error handling for invalid input"
    }
  ],
  "coverage_analysis": {
    "scenarios_covered": [
      "Single email extraction",
      "Multiple emails",
      "Complex email formats (subdomains, plus-addressing)",
      "No emails present",
      "Invalid email patterns",
      "Empty input"
    ],
    "potential_gaps": [
      "Internationalized email addresses (unicode domains)",
      "Very long text with many emails (performance)"
    ],
    "recommendations": [
      "Consider adding example with 10+ emails to test scalability"
    ]
  }
}
```

---

## Usage Notes

- **When to use**: After prompt is drafted, before deployment
- **When NOT to use**: For trivial prompts with obvious behavior
- **Best practice**: Test prompt with generated examples to validate
- **Iteration**: If prompt fails on examples, revise prompt first, then examples

---

## Quality Checklist

- [ ] 3-5 examples generated
- [ ] At least 1 normal, 1 edge, 1 error scenario
- [ ] Examples increase in complexity
- [ ] Outputs match prompt's format exactly
- [ ] Rationale explains why each example is valuable
- [ ] Coverage analysis identifies gaps
- [ ] Examples are realistic and diverse
