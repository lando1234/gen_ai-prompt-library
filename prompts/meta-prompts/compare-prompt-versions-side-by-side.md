---
type: meta-prompt
complexity: intermediate
context: technical
output_format: structured
interaction_mode: single-shot
production_ready: experimental
tags: [comparison, analysis, optimization, meta-prompting, decision-support]
---

# Compare Multiple Prompt Versions Side-by-Side

## Purpose
Perform comparative analysis of 2-3 prompt variants, listing pros/cons and ideal use cases for each to support informed decision-making.

## Context
Use this meta-prompt when:
- A/B testing different prompt approaches
- Choosing between alternative implementations
- Documenting trade-offs for team alignment
- Evaluating prompt refactoring results

---

## Prompt

You are a prompt comparison analyst. Your task is to perform objective side-by-side analysis of prompt variants, highlighting strengths, weaknesses, and optimal use cases.

**INPUT**: 2-3 prompt variants to compare.

**PROCESS**:

1. **Normalize Comparison Dimensions**:
   - **Clarity**: How clear are instructions?
   - **Completeness**: Are all requirements covered?
   - **Efficiency**: Token count and processing complexity
   - **Robustness**: Handles edge cases and errors?
   - **Maintainability**: Easy to update and debug?
   - **Flexibility**: Adaptable to variations?

2. **Analyze Each Variant**:
   - Identify unique features and approaches
   - Measure token count
   - Assess complexity and cognitive load
   - Test against common inputs (mentally or with examples)
   - Note implicit assumptions

3. **Create Comparison Matrix**:
   - Side-by-side dimension ratings
   - Pros and cons for each
   - Ideal use cases and scenarios
   - Trade-off analysis

4. **Provide Recommendation**:
   - Context-dependent suggestion
   - If tied, explain when to use each
   - Flag if testing needed to decide

**OUTPUT FORMAT**:
```json
{
  "comparison_summary": {
    "variants_count": <2 or 3>,
    "primary_differences": ["difference 1", "difference 2"],
    "comparison_dimensions": ["clarity", "completeness", "efficiency", "robustness", "maintainability", "flexibility"]
  },
  "variant_analysis": [
    {
      "variant_id": "A|B|C",
      "variant_name": "<descriptive name>",
      "token_count": <number>,
      "complexity_level": "low|medium|high",
      "scores": {
        "clarity": <1-5>,
        "completeness": <1-5>,
        "efficiency": <1-5>,
        "robustness": <1-5>,
        "maintainability": <1-5>,
        "flexibility": <1-5>
      },
      "strengths": ["strength 1", "strength 2"],
      "weaknesses": ["weakness 1", "weakness 2"],
      "ideal_for": ["use case 1", "use case 2"],
      "avoid_when": ["scenario 1", "scenario 2"]
    }
  ],
  "comparison_matrix": {
    "dimension": "<dimension name>",
    "A": "<score and note>",
    "B": "<score and note>",
    "C": "<score and note if applicable>",
    "winner": "<variant with best score>"
  },
  "trade_off_analysis": [
    {
      "trade_off": "<what you gain vs. lose>",
      "variants_affected": ["A", "B"],
      "decision_guidance": "<when to prioritize what>"
    }
  ],
  "recommendation": {
    "primary_choice": "A|B|C",
    "rationale": "<why this is recommended>",
    "context_dependencies": ["depends on factor 1", "depends on factor 2"],
    "alternative_scenarios": {
      "B": "<when to use B instead>",
      "C": "<when to use C instead>"
    },
    "testing_needed": "<yes/no and what to test>"
  }
}
```

**CONSTRAINTS**:
- Must compare exactly 2-3 variants (not more)
- Scores must be objective with justification
- Recommendations must acknowledge context-dependency
- If no clear winner, explain trade-offs
- Flag if real-world testing is needed

---

## Example

**Input Prompts**:

**Variant A** (Verbose):
```
You are an expert code reviewer with 10+ years of experience. Please carefully and thoroughly review the following code. Look for bugs, security vulnerabilities, performance issues, and style problems. Provide detailed feedback with specific line numbers and suggestions for improvement. Be constructive and educational in your tone.
```

**Variant B** (Concise):
```
Review this code for bugs, security, performance, and style. List issues with line numbers and fixes.
```

**Variant C** (Structured):
```
You are a code reviewer. Analyze the code below.

OUTPUT:
```json
{
  "bugs": [{"line": N, "issue": "", "fix": ""}],
  "security": [...],
  "performance": [...],
  "style": [...]
}
```
```

**Output**:
```json
{
  "comparison_summary": {
    "variants_count": 3,
    "primary_differences": [
      "Verbosity: A is explicit, B is minimal, C is structured",
      "Output format: A/B are free-form, C enforces JSON"
    ],
    "comparison_dimensions": ["clarity", "completeness", "efficiency", "robustness", "maintainability", "flexibility"]
  },
  "variant_analysis": [
    {
      "variant_id": "A",
      "variant_name": "Verbose with Context",
      "token_count": 87,
      "complexity_level": "low",
      "scores": {
        "clarity": 5,
        "completeness": 5,
        "efficiency": 2,
        "robustness": 4,
        "maintainability": 3,
        "flexibility": 5
      },
      "strengths": [
        "Very clear expectations (experience level, tone, scope)",
        "Explicit about constructive feedback",
        "Flexible output format"
      ],
      "weaknesses": [
        "High token count (87) increases API cost",
        "Redundant phrases ('carefully and thoroughly')",
        "May generate verbose responses"
      ],
      "ideal_for": [
        "Onboarding junior developers",
        "Educational contexts where explanation is valued",
        "When output format can vary"
      ],
      "avoid_when": [
        "Processing high volumes (cost)",
        "Need structured/parseable output",
        "Token limits are tight"
      ]
    },
    {
      "variant_id": "B",
      "variant_name": "Minimal/Concise",
      "token_count": 23,
      "complexity_level": "low",
      "scores": {
        "clarity": 3,
        "completeness": 3,
        "efficiency": 5,
        "robustness": 2,
        "maintainability": 4,
        "flexibility": 4
      },
      "strengths": [
        "Very efficient (23 tokens, 74% reduction)",
        "Fast processing",
        "Easy to maintain"
      ],
      "weaknesses": [
        "Lacks context on tone/depth",
        "No guidance on output format",
        "May miss nuanced issues"
      ],
      "ideal_for": [
        "High-volume processing",
        "Cost-sensitive applications",
        "When context is understood"
      ],
      "avoid_when": [
        "Users need guidance on depth/tone",
        "Output needs consistent structure",
        "Complex or safety-critical code"
      ]
    },
    {
      "variant_id": "C",
      "variant_name": "Structured JSON Output",
      "token_count": 52,
      "complexity_level": "medium",
      "scores": {
        "clarity": 4,
        "completeness": 5,
        "efficiency": 4,
        "robustness": 5,
        "maintainability": 5,
        "flexibility": 2
      },
      "strengths": [
        "Enforces structured output (parseable)",
        "Clear categorization of issues",
        "Easy to integrate with tooling",
        "Prevents ambiguous responses"
      ],
      "weaknesses": [
        "Rigid format (less flexible)",
        "May truncate nuanced explanations",
        "Requires JSON parsing"
      ],
      "ideal_for": [
        "Automated code review pipelines",
        "CI/CD integration",
        "When output is machine-processed",
        "Need for consistent formatting"
      ],
      "avoid_when": [
        "Human-only consumption",
        "Need flexible/narrative feedback",
        "LLM struggles with JSON compliance"
      ]
    }
  ],
  "comparison_matrix": [
    {
      "dimension": "Clarity",
      "A": "5 - Very explicit",
      "B": "3 - Minimal context",
      "C": "4 - Clear structure",
      "winner": "A"
    },
    {
      "dimension": "Efficiency",
      "A": "2 - High token count",
      "B": "5 - Very lean",
      "C": "4 - Moderate",
      "winner": "B"
    },
    {
      "dimension": "Robustness",
      "A": "4 - Good coverage",
      "B": "2 - Lacks guidance",
      "C": "5 - Structured validation",
      "winner": "C"
    }
  ],
  "trade_off_analysis": [
    {
      "trade_off": "Token efficiency vs. Clarity",
      "variants_affected": ["A", "B"],
      "decision_guidance": "Use A for education/onboarding, B for high-volume production"
    },
    {
      "trade_off": "Flexibility vs. Consistency",
      "variants_affected": ["A", "C"],
      "decision_guidance": "Use A for human review, C for automation"
    },
    {
      "trade_off": "Cost vs. Quality",
      "variants_affected": ["B", "C"],
      "decision_guidance": "B is cheapest but less robust; C balances cost and structure"
    }
  ],
  "recommendation": {
    "primary_choice": "C",
    "rationale": "Best balance of efficiency, robustness, and maintainability for production use. Structured output enables automation and consistent quality.",
    "context_dependencies": [
      "Assumes code review is automated or semi-automated",
      "Assumes output is parsed by tools (not just humans)",
      "Assumes moderate token budget"
    ],
    "alternative_scenarios": {
      "A": "Use for educational code reviews, onboarding, or when reviewer needs flexibility in feedback style",
      "B": "Use for extremely high-volume processing (>100K/day) where cost is primary concern and output quality can vary"
    },
    "testing_needed": "Yes - run A/B test with 100 real code samples to compare output quality and user satisfaction"
  }
}
```

---

## Usage Notes

- **When to use**: Choosing between alternatives, A/B testing, refactoring decisions
- **When NOT to use**: Only one prompt exists (nothing to compare)
- **Best practice**: Test top 2 variants with real data before deciding
- **Iteration**: Re-compare after optimizations

---

## Quality Checklist

- [ ] 2-3 variants compared (not more)
- [ ] All dimensions scored objectively with justification
- [ ] Pros and cons specific and actionable
- [ ] Ideal use cases clearly defined
- [ ] Trade-offs explicitly analyzed
- [ ] Recommendation is context-aware
- [ ] Testing guidance provided if needed
- [ ] Comparison matrix shows clear winners per dimension
