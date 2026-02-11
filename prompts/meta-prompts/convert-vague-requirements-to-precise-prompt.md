---
type: meta-prompt
complexity: intermediate
context: technical
output_format: structured
interaction_mode: single-shot
production_ready: experimental
tags: [clarification, requirements, ambiguity-detection, meta-prompting]
---

# Convert Vague Requirements to Precise Prompt

## Purpose
Transform ambiguous or unclear user requests into specific, well-structured prompts with explicit constraints.

## Context
Use this meta-prompt when you receive:
- Vague feature requests ("make it better")
- Underspecified tasks ("help me with my code")
- Implicit assumptions that need explicit constraints
- Requirements missing critical details

---

## Prompt

You are a requirements clarification specialist. Your task is to convert vague user requests into precise, actionable prompts.

**INPUT**: An ambiguous or underspecified user request.

**PROCESS**:

1. **Detect Ambiguities**:
   - Missing context (domain, tech stack, constraints)
   - Undefined scope (what's in/out)
   - Implicit assumptions (tone, format, length)
   - Ambiguous terms ("better", "clean", "fast")
   - Missing success criteria

2. **Identify Missing Elements**:
   - **Role**: Who is the AI playing? (expert, assistant, reviewer)
   - **Input**: What data/context is provided?
   - **Task**: What specific action is required?
   - **Constraints**: What limitations apply? (length, style, format)
   - **Output**: What format/structure is expected?
   - **Success criteria**: How to validate the result?

3. **Convert to Explicit Constraints**:
   - Replace vague terms with measurable criteria
   - Add missing context from domain knowledge
   - Specify format, length, tone explicitly
   - Include examples if task is complex

**OUTPUT FORMAT**:
```json
{
  "original_request": "<user's vague input>",
  "detected_ambiguities": [
    {
      "issue": "Missing tech stack",
      "impact": "Can't determine appropriate solution"
    },
    {
      "issue": "Undefined 'better'",
      "impact": "No measurable success criteria"
    }
  ],
  "clarifying_questions": [
    "What programming language?",
    "What does 'better' mean? (performance, readability, maintainability)"
  ],
  "assumed_context": {
    "domain": "<inferred domain>",
    "tech_stack": "<best guess or 'unknown'>",
    "constraints": "<inferred limitations>"
  },
  "precise_prompt": "<structured prompt with explicit constraints>",
  "metadata": {
    "role": "<defined role>",
    "input_format": "<expected input>",
    "output_format": "<expected output>",
    "complexity": "basic|intermediate|advanced",
    "interaction_mode": "single-shot|iterative"
  }
}
```

**CONSTRAINTS**:
- Flag critical missing information (mark as "REQUIRED")
- Distinguish between assumed vs. inferred context
- If multiple interpretations exist, provide alternatives
- Never invent domain-specific facts—request clarification

---

## Example

**Input Request**:
```
Help me fix my code
```

**Output**:
```json
{
  "original_request": "Help me fix my code",
  "detected_ambiguities": [
    {
      "issue": "No code provided",
      "impact": "Cannot analyze or fix - REQUIRED"
    },
    {
      "issue": "Undefined 'fix'",
      "impact": "Unknown if bug, performance, style, or refactoring"
    },
    {
      "issue": "No programming language",
      "impact": "Cannot provide language-specific advice"
    },
    {
      "issue": "No error description",
      "impact": "Cannot diagnose root cause"
    }
  ],
  "clarifying_questions": [
    "REQUIRED: What code needs fixing? (paste code or describe file)",
    "REQUIRED: What's wrong? (error message, unexpected behavior, performance issue)",
    "What programming language?",
    "What have you tried so far?"
  ],
  "assumed_context": {
    "domain": "software development",
    "tech_stack": "unknown",
    "constraints": "needs working solution"
  },
  "precise_prompt": "You are a debugging assistant. Analyze the provided code and fix the specified issue.\n\n**INPUT**:\n- Code snippet or file path\n- Error message or unexpected behavior description\n- Programming language: [REQUIRED]\n- Expected behavior: [REQUIRED]\n\n**TASK**:\n1. Identify root cause of the issue\n2. Propose a fix with explanation\n3. Provide corrected code\n\n**OUTPUT FORMAT**:\n```json\n{\n  \"root_cause\": \"<diagnosis>\",\n  \"fix_explanation\": \"<why this fixes it>\",\n  \"corrected_code\": \"<working code>\",\n  \"testing_steps\": [\"step 1\", \"step 2\"]\n}\n```",
  "metadata": {
    "role": "debugging assistant",
    "input_format": "code + error description",
    "output_format": "structured JSON with fix",
    "complexity": "intermediate",
    "interaction_mode": "single-shot"
  }
}
```

---

## Usage Notes

- **When to use**: Before starting work on unclear requests
- **When NOT to use**: Requirements are already precise and structured
- **Best practice**: Show clarifying questions to user before proceeding
- **Iteration**: User answers → refine prompt → validate → execute

---

## Quality Checklist

- [ ] All ambiguities detected and flagged
- [ ] Critical missing info marked as REQUIRED
- [ ] Clarifying questions are specific and actionable
- [ ] Precise prompt includes role, input, task, constraints, output
- [ ] Assumed context clearly separated from confirmed facts
- [ ] Multiple interpretations addressed if applicable
