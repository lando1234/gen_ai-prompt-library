---
type: meta-prompt
complexity: intermediate
context: technical
output_format: structured
interaction_mode: single-shot
production_ready: experimental
tags: [debugging, troubleshooting, diagnosis, meta-prompting, fix-suggestions]
---

# Diagnose Why Prompt Is Not Working

## Purpose
Perform systematic troubleshooting of failing prompts (ambiguity, context overload, conflicting instructions) and provide prioritized fix suggestions.

## Context
Use this meta-prompt when:
- Prompt produces unexpected or incorrect outputs
- LLM ignores instructions or constraints
- Output quality is inconsistent
- Performance degrades unexpectedly

---

## Prompt

You are a prompt debugging specialist. Your task is to systematically diagnose why a prompt is failing and provide actionable fixes.

**INPUT**:
- A failing prompt
- Description of failure mode (what's wrong with outputs)
- Optional: Example inputs and actual vs. expected outputs

**PROCESS**:

1. **Categorize Failure Type**:
   - **Ambiguity**: Instructions unclear or contradictory
   - **Context overload**: Too much information to process
   - **Constraint violation**: LLM ignores rules
   - **Format compliance**: Wrong output structure
   - **Quality issues**: Correct format but poor content
   - **Edge case failure**: Works for common cases, fails on edge cases

2. **Systematic Diagnosis**:

   **Check for Ambiguity**:
   - Vague terms ("good", "clean", "efficient")
   - Missing definitions
   - Implicit assumptions
   - Multiple interpretations possible

   **Check for Conflicts**:
   - Contradictory requirements
   - Impossible constraints
   - Competing priorities

   **Check for Overload**:
   - Prompt too long (>2000 tokens)
   - Too many instructions
   - Complex nested logic
   - Excessive examples

   **Check for Constraint Weakness**:
   - Constraints stated once (not reinforced)
   - Positive instructions only (no "NEVER" statements)
   - No validation or examples

   **Check for Format Issues**:
   - Format not explicitly specified
   - No schema or template provided
   - Examples don't match instructions

3. **Root Cause Analysis**:
   - Identify primary failure cause
   - Map symptoms to specific prompt sections
   - Determine if LLM limitation or prompt issue

4. **Generate Fixes**:
   - Prioritize by impact (high → low)
   - Provide before/after examples
   - Explain why fix addresses root cause

**OUTPUT FORMAT**:
```json
{
  "failure_analysis": {
    "primary_failure_type": "ambiguity|context_overload|constraint_violation|format_compliance|quality|edge_case",
    "symptoms": ["symptom 1", "symptom 2"],
    "affected_outputs": {
      "what_should_happen": "<expected behavior>",
      "what_actually_happens": "<observed behavior>",
      "pattern": "<when/how often failure occurs>"
    }
  },
  "diagnostic_findings": [
    {
      "issue_id": "<unique id>",
      "issue_type": "ambiguity|conflict|overload|weak_constraint|format_issue|other",
      "severity": "critical|high|medium|low",
      "location": "<where in prompt this issue appears>",
      "description": "<what's wrong>",
      "evidence": "<specific text or pattern>",
      "impact": "<how this causes the failure>",
      "likely_root_cause": true|false
    }
  ],
  "root_cause_summary": {
    "primary_cause": "<main reason for failure>",
    "contributing_factors": ["factor 1", "factor 2"],
    "confidence": "high|medium|low"
  },
  "recommended_fixes": [
    {
      "fix_id": "<unique id>",
      "priority": "critical|high|medium|low",
      "addresses_issues": ["issue_id_1", "issue_id_2"],
      "fix_type": "clarify|remove_conflict|simplify|reinforce_constraint|add_format|add_example",
      "description": "<what to change>",
      "before": "<problematic prompt section>",
      "after": "<fixed prompt section>",
      "rationale": "<why this fixes the issue>",
      "expected_improvement": "<how output should improve>",
      "implementation_effort": "easy|medium|hard"
    }
  ],
  "fix_prioritization": {
    "apply_first": ["fix_id"],
    "then_if_needed": ["fix_id"],
    "optional_enhancements": ["fix_id"],
    "rationale": "<why this order>"
  },
  "validation_plan": {
    "test_cases": [
      {
        "input": "<test input>",
        "expected_output": "<what should happen after fixes>",
        "validates": ["fix_id"]
      }
    ],
    "success_criteria": "<when to consider fixes successful>",
    "rollback_trigger": "<when to revert changes>"
  }
}
```

**CONSTRAINTS**:
- Diagnose root cause, not just symptoms
- Provide specific fixes (not generic advice)
- Prioritize by impact and effort
- Include validation approach
- Be honest about uncertainty (don't guess)

---

## Example

**Input Prompt** (failing):
```
You are a helpful assistant. Generate clean, efficient Python code based on user requirements. Make sure it's well-documented and follows best practices. Handle errors gracefully.
```

**Failure Mode**: Generated code lacks docstrings, doesn't handle edge cases, varies in style

**Output**:
```json
{
  "failure_analysis": {
    "primary_failure_type": "ambiguity",
    "symptoms": [
      "Inconsistent docstring presence",
      "Edge cases not handled",
      "Style varies (sometimes verbose, sometimes terse)"
    ],
    "affected_outputs": {
      "what_should_happen": "Code with consistent docstrings, error handling, and style",
      "what_actually_happens": "Code quality varies; sometimes missing docs or error handling",
      "pattern": "Fails more often on complex requests; simple functions usually OK"
    }
  },
  "diagnostic_findings": [
    {
      "issue_id": "AMB-1",
      "issue_type": "ambiguity",
      "severity": "critical",
      "location": "'clean, efficient' and 'well-documented'",
      "description": "Vague qualifiers without measurable criteria",
      "evidence": "'clean', 'efficient', 'well-documented', 'best practices' are subjective",
      "impact": "LLM has no objective standard to follow, leading to inconsistent quality",
      "likely_root_cause": true
    },
    {
      "issue_id": "AMB-2",
      "issue_type": "ambiguity",
      "severity": "high",
      "location": "'Handle errors gracefully'",
      "description": "No specification of what 'gracefully' means",
      "evidence": "No examples of error handling; 'gracefully' is vague",
      "impact": "LLM sometimes adds try/except, sometimes ignores errors",
      "likely_root_cause": false
    },
    {
      "issue_id": "WEAK-1",
      "issue_type": "weak_constraint",
      "severity": "high",
      "location": "Documentation requirement",
      "description": "Documentation mentioned once without specification",
      "evidence": "'well-documented' stated but not defined (docstrings? comments? what format?)",
      "impact": "LLM inconsistently adds documentation",
      "likely_root_cause": true
    },
    {
      "issue_id": "FORMAT-1",
      "issue_type": "format_issue",
      "severity": "medium",
      "location": "Entire prompt lacks structure",
      "description": "No output format or schema specified",
      "evidence": "No explicit structure like 'OUTPUT: <code> <explanation>'",
      "impact": "Output format varies (code only, code+explanation, code+tests)",
      "likely_root_cause": false
    },
    {
      "issue_id": "MISSING-1",
      "issue_type": "other",
      "severity": "medium",
      "location": "No examples provided",
      "description": "No few-shot examples showing expected quality",
      "evidence": "Prompt has no examples of 'clean, documented' code",
      "impact": "LLM has no reference for what 'best practices' means",
      "likely_root_cause": false
    }
  ],
  "root_cause_summary": {
    "primary_cause": "Vague qualifiers ('clean', 'efficient', 'well-documented') without measurable criteria",
    "contributing_factors": [
      "Weak constraint reinforcement (documentation mentioned once)",
      "No output format specification",
      "No examples to demonstrate expected quality"
    ],
    "confidence": "high"
  },
  "recommended_fixes": [
    {
      "fix_id": "FIX-1",
      "priority": "critical",
      "addresses_issues": ["AMB-1", "WEAK-1"],
      "fix_type": "clarify",
      "description": "Replace vague terms with explicit criteria",
      "before": "'clean, efficient' and 'well-documented'",
      "after": "**CODE REQUIREMENTS**:\n- Docstrings: Google style for all functions/classes\n- Type hints: Required for all parameters and returns\n- Complexity: O(n²) or better, avoid nested loops where possible\n- Line length: Max 88 characters (Black formatter)\n- Comments: Explain 'why', not 'what'",
      "rationale": "Measurable criteria eliminate ambiguity; LLM can objectively follow rules",
      "expected_improvement": "Consistent docstring format, predictable code style",
      "implementation_effort": "easy"
    },
    {
      "fix_id": "FIX-2",
      "priority": "high",
      "addresses_issues": ["AMB-2"],
      "fix_type": "clarify",
      "description": "Specify error handling approach explicitly",
      "before": "'Handle errors gracefully'",
      "after": "**ERROR HANDLING**:\n- Validate inputs: Check types and ranges at function start\n- Use specific exceptions: ValueError for invalid input, TypeError for wrong types\n- No bare except: Always catch specific exceptions\n- Example: if n < 0: raise ValueError('n must be non-negative')",
      "rationale": "Concrete examples + specific rules = consistent error handling",
      "expected_improvement": "All generated code includes proper input validation",
      "implementation_effort": "easy"
    },
    {
      "fix_id": "FIX-3",
      "priority": "high",
      "addresses_issues": ["FORMAT-1"],
      "fix_type": "add_format",
      "description": "Add explicit output format section",
      "before": "(no output format specified)",
      "after": "**OUTPUT FORMAT**:\n```python\n# Code here with docstrings and type hints\n```\n\n**EXPLANATION**: Brief description of approach and complexity",
      "rationale": "Explicit format ensures consistent structure",
      "expected_improvement": "Output always includes code + explanation",
      "implementation_effort": "easy"
    },
    {
      "fix_id": "FIX-4",
      "priority": "medium",
      "addresses_issues": ["MISSING-1"],
      "fix_type": "add_example",
      "description": "Add few-shot example showing expected quality",
      "before": "(no examples)",
      "after": "**EXAMPLE**:\nUser: 'Calculate factorial'\nOutput:\n```python\ndef factorial(n: int) -> int:\n    \"\"\"Calculate factorial using recursion.\n    \n    Args:\n        n: Non-negative integer\n        \n    Returns:\n        Factorial of n\n        \n    Raises:\n        ValueError: If n < 0\n    \"\"\"\n    if n < 0:\n        raise ValueError('n must be non-negative')\n    return 1 if n <= 1 else n * factorial(n - 1)\n```\n\n**EXPLANATION**: Recursive approach, O(n) time, O(n) space. Handles base cases and validates input.",
      "rationale": "Concrete example demonstrates all requirements in action",
      "expected_improvement": "LLM follows example pattern more consistently",
      "implementation_effort": "medium"
    }
  ],
  "fix_prioritization": {
    "apply_first": ["FIX-1"],
    "then_if_needed": ["FIX-2", "FIX-3"],
    "optional_enhancements": ["FIX-4"],
    "rationale": "FIX-1 addresses root cause (ambiguity). FIX-2 and FIX-3 address high-impact issues. FIX-4 is helpful but prompt should work without it after clarifications."
  },
  "validation_plan": {
    "test_cases": [
      {
        "input": "Generate a function to calculate Fibonacci numbers",
        "expected_output": "Code with Google-style docstring, type hints, input validation, O(n) or better complexity, explanation of approach",
        "validates": ["FIX-1", "FIX-2", "FIX-3"]
      },
      {
        "input": "Create a function to merge two sorted lists",
        "expected_output": "Same quality standards applied consistently",
        "validates": ["FIX-1", "FIX-3"]
      },
      {
        "input": "Write a function with edge case: empty input",
        "expected_output": "Explicit handling of empty input with appropriate error/return",
        "validates": ["FIX-2"]
      }
    ],
    "success_criteria": "3/3 test cases produce code meeting all criteria (docstrings, type hints, error handling, explanation)",
    "rollback_trigger": "If quality gets worse or compliance drops below 80%"
  }
}
```

---

## Usage Notes

- **When to use**: Prompt produces wrong/inconsistent outputs
- **When NOT to use**: For prompts that haven't been tested yet
- **Best practice**: Test fixes incrementally (one at a time) to isolate impact
- **Iteration**: Re-diagnose if fixes don't resolve issues

---

## Quality Checklist

- [ ] Failure type correctly categorized
- [ ] Root cause identified (not just symptoms)
- [ ] All diagnostic findings have evidence
- [ ] Fixes are specific with before/after examples
- [ ] Fixes prioritized by impact and effort
- [ ] Validation plan includes test cases
- [ ] Success criteria are measurable
- [ ] Confidence level assessed honestly
