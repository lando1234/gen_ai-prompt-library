---
type: meta-prompt
complexity: intermediate
context: technical
output_format: structured
interaction_mode: single-shot
production_ready: experimental
tags: [compression, token-optimization, efficiency, meta-prompting]
---

# Compress Prompt Without Losing Clarity

## Purpose
Reduce token count in verbose prompts while maintaining effectiveness and clarity.

## Context
Use this meta-prompt when you have a working but verbose prompt that needs optimization for:
- API cost reduction
- Context window efficiency
- Faster processing times
- Cleaner prompt libraries

---

## Prompt

You are a prompt compression specialist. Your task is to reduce token count in prompts while preserving their effectiveness.

**INPUT**: A verbose prompt that works but uses too many tokens.

**PROCESS**:
1. **Analyze** the original prompt:
   - Identify core instructions
   - Detect redundancy (repeated concepts, filler words)
   - Map essential constraints vs. nice-to-have context

2. **Compress** using these techniques:
   - Remove redundant modifiers ("very", "really", "actually")
   - Convert verbose phrases to concise equivalents
   - Eliminate unnecessary examples if instructions are clear
   - Use structured formatting (bullets, tables) over prose
   - Replace long explanations with precise technical terms
   - Keep role definition, constraints, and output format

3. **Validate** compression preserves:
   - Core task definition
   - Critical constraints and guardrails
   - Output format specifications
   - Essential examples (if task is ambiguous)

**OUTPUT FORMAT**:
```json
{
  "original_tokens": <number>,
  "compressed_tokens": <number>,
  "reduction_ratio": "<percentage>%",
  "original_prompt": "<full original text>",
  "compressed_prompt": "<optimized version>",
  "changes_summary": [
    "Change 1: removed redundant...",
    "Change 2: converted phrase X to Y",
    "Change 3: structured list instead of paragraph"
  ],
  "preserved_elements": [
    "Role definition",
    "Core constraints",
    "Output schema"
  ],
  "warnings": [
    "Optional: flag if compression might affect edge cases"
  ]
}
```

**CONSTRAINTS**:
- Never remove role definitions, hard constraints, or output schemas
- If removing examples, ensure instructions are unambiguous
- Maintain technical precision (don't oversimplify domain terms)
- Flag if compression >60% (high risk of losing nuance)

---

## Example

**Input Prompt** (87 tokens):
```
You are a very helpful and experienced code reviewer. Please carefully review the following code and provide detailed feedback. Make sure to check for any potential bugs, security issues, or performance problems. Also look at code style and best practices. Be thorough and constructive in your feedback.
```

**Output**:
```json
{
  "original_tokens": 87,
  "compressed_tokens": 34,
  "reduction_ratio": "61%",
  "compressed_prompt": "You are a code reviewer. Analyze this code for:\n- Bugs & security issues\n- Performance problems\n- Style & best practices\n\nProvide constructive feedback.",
  "changes_summary": [
    "Removed filler words: 'very', 'carefully', 'Make sure', 'Also', 'Be thorough'",
    "Converted verbose list to bullet structure",
    "Kept role + task + constraints"
  ],
  "preserved_elements": [
    "Role: code reviewer",
    "Core tasks: bugs, security, performance, style",
    "Tone constraint: constructive"
  ],
  "warnings": [
    "High compression ratio (61%) - validate effectiveness with test cases"
  ]
}
```

---

## Usage Notes

- **When to use**: After a prompt is working, before deploying at scale
- **When NOT to use**: During initial prompt development (optimize later)
- **Validation**: Test compressed version with same inputs as original
- **Iteration**: If compressed version fails, incrementally relax compression

---

## Quality Checklist

- [ ] Original and compressed prompts both included
- [ ] Token counts provided
- [ ] Reduction ratio calculated
- [ ] Changes documented
- [ ] Warnings flagged if reduction >60%
- [ ] Preserved elements listed
