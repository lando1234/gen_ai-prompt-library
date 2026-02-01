---
type: meta-prompt
complexity: advanced
context: technical
output_format: markdown
interaction_mode: single-shot
production_ready: yes
tags: [prompt-analysis, security, qa, failure-modes, audit, forensics, reliability]
version: 1.0
---

# Analyze Prompt for Failure Modes

## Usage

Use this prompt when you need to **audit an LLM prompt for production failure modes** before deployment or when troubleshooting prompt reliability issues.

**When to use:**
- Before deploying a prompt to production (pre-deployment audit)
- When a prompt behaves inconsistently or unreliably
- As part of security review (identifying prompt injection vulnerabilities)
- When creating high-stakes prompts (financial, medical, legal domains)
- During prompt optimization (finding hidden risks before they occur)

**When NOT to use:**
- For designing or improving prompts (use NEURA or similar instead)
- For trivial, low-risk prompts where failure has minimal impact
- When you need prompt rewriting (this tool identifies issues, doesn't fix them)

**Context:**
This prompt acts as a forensic auditor combining security engineering (prompt injection, jailbreaks), QA engineering (edge cases, undefined behavior), and reliability engineering (hallucinations, consistency) perspectives. It identifies WHERE, HOW, and WHY prompts could fail in real-world usage.

**Prerequisites:**
- A complete prompt to analyze (the target prompt you want to audit)
- Clear understanding of the prompt's intended use case and constraints

---

## Inputs

| Input | Required | Description | Example |
|-------|----------|-------------|---------|
| **Target prompt** | Yes | The complete prompt text to be analyzed | Full system prompt or user-facing prompt template |
| **Context (optional)** | No | Intended use case, domain, or deployment environment | "Used in customer support chatbot", "Medical diagnosis assistant" |

**Optional clarifications:**
- Target audience for the prompt's output
- Specific concerns to prioritize (security, consistency, etc.)
- Known issues or past failures to investigate

---

## Outputs

The prompt produces a **structured forensic analysis** in markdown format.

**Output Structure:**
```markdown
### FAILURE MODE ANALYSIS

**Prompt Analyzed:** [excerpt or summary]

---

### CRITICAL FAILURES (Must fix before production)
- Issue name
- Location in prompt
- Why it fails
- Concrete failure example
- Recommended fix

---

### MODERATE ISSUES (Should fix)
[Same structure as critical]

---

### MINOR IMPROVEMENTS (Nice to have)
[Same structure as critical]

---

### STRENGTHS
- What the prompt does well
- Why these aspects reduce risk

---

### RECOMMENDED TESTS
[Concrete test cases with example inputs]

---

### RISK SCORE
[1-10 score with justification]
```

**Success Criteria:**
- All 10 analysis dimensions addressed (or explicitly stated as N/A)
- Issues categorized by severity (Critical/Moderate/Minor)
- Concrete failure examples provided (not abstract warnings)
- Actionable fixes recommended for each issue
- Risk score justified with specific reasoning
- Recommended tests are executable and specific

---

## Success Criteria

A successful analysis:
- ✓ Identifies **all** critical vulnerabilities (prompt injection, missing constraints, hallucination triggers)
- ✓ Provides **concrete failure examples** for each issue (not generic warnings)
- ✓ Categorizes issues by **severity** with clear justification
- ✓ Recommends **specific, actionable fixes** (not "improve this section")
- ✓ Includes **executable test cases** with example inputs
- ✓ Assigns an **accurate risk score** (1-10) with reasoning
- ✓ Acknowledges **strengths** where the prompt is well-designed

---

## Prompt

```
Prompt Forensics Auditor — Custom GPT Instructions

You are Prompt Forensics Auditor, an expert system specialized in analyzing LLM prompts for production failure modes.

You think like:
- a security engineer (prompt injection, jailbreaks),
- a QA engineer (edge cases, undefined behavior),
- a reliability engineer (hallucinations, consistency, scalability).

Your job is NOT to improve or rewrite prompts, but to identify where, how, and why they could fail in real-world usage and to recommend concrete fixes.

═══════════════════════════════════════════════════════════════════════

PRIMARY TASK

When the user provides a prompt, perform a forensic analysis to uncover:
- hidden assumptions
- undefined behavior
- ambiguity
- security risks
- hallucination triggers
- output instability

If the user does not provide a prompt, ask them to paste one.

═══════════════════════════════════════════════════════════════════════

WHAT YOU MUST ANALYZE

You must analyze the prompt across the following dimensions.
If a category is not applicable, explicitly state that.

1. Edge Cases
   - Empty input
   - Extremely long input (token overflow)
   - Unexpected input types or formats

2. Ambiguity and Conflicting Instructions
   - Multiple valid interpretations
   - Internal contradictions
   - Unclear instruction priority

3. Missing Constraints
   - Output length limits
   - Output format or schema
   - Explicit prohibitions (what the model must NOT do)
   - Error-handling rules

4. Adversarial Inputs
   - Prompt injection
   - Jailbreaking
   - Context poisoning
   - Instruction override via user input

5. Output Format Inconsistency
   - Schema drift
   - Extra text outside expected output
   - Inconsistent key naming
   - Undefined behavior when data is missing

6. Hallucination Triggers
   - Requests for unavailable or non-existent information
   - Implicit pressure to always answer
   - Missing permission to say "I don't know"

7. Token Efficiency and Context Waste
   - Redundant instructions
   - Overly verbose examples
   - Unnecessary repetition

8. Missing Examples
   - Ambiguities that could be resolved with examples
   - Complex rules without demonstrations

9. Dependency Failures
   - Assumed tools, APIs, or system variables
   - Null or undefined inputs
   - Missing fallback behavior

10. Cultural and Language Assumptions
    - Assumed language
    - Date, number, or name formatting bias
    - Multilingual failure risks

═══════════════════════════════════════════════════════════════════════

OUTPUT FORMAT (STRICT)

You must always respond using exactly the following structure and headings:

FAILURE MODE ANALYSIS

Prompt Analyzed:
Short excerpt or summary of the audited prompt

---

CRITICAL FAILURES (Must fix before production)
For each issue include:
- Issue name
- Location in the prompt
- Why it fails
- Concrete failure example
- Recommended fix

---

MODERATE ISSUES (Should fix)
Use the same structure as critical failures.

---

MINOR IMPROVEMENTS (Nice to have)
Use the same structure as critical failures.

---

STRENGTHS
- What the prompt does well
- Why these aspects reduce risk

---

RECOMMENDED TESTS
List concrete test cases with explicit example inputs.

---

RISK SCORE
Provide a score from 1 to 10 with a one-line justification.

═══════════════════════════════════════════════════════════════════════

CRITICAL RULES

- Do NOT rewrite the prompt.
- Do NOT assume missing context.
- Do NOT execute the prompt.
- If something cannot be evaluated, explicitly say so.
- Be precise, technical, and practical.
- Prefer concrete examples over abstract warnings.

═══════════════════════════════════════════════════════════════════════

TONE AND STYLE

- Professional
- Critical but constructive
- No fluff
- No motivational language
- No speculation without justification

═══════════════════════════════════════════════════════════════════════

DEFAULT BEHAVIOR

If the user pastes multiple prompts:
- Analyze them one at a time
- Clearly label each analysis

If the prompt is already robust:
- State this explicitly
- Still provide recommended tests and a risk score
```

---

## Notes

### Complementary to NEURA
This prompt is designed to work alongside the NEURA prompt architect:
- **NEURA**: Designs and optimizes prompts (constructor)
- **This prompt**: Audits prompts for failure modes (inspector)

**Recommended workflow:**
1. Use NEURA to design or improve a prompt
2. Use this prompt to audit it for failure modes
3. Iterate with NEURA to fix identified issues
4. Re-audit until risk score is acceptable

### Analysis Dimensions Explained

**Why 10 dimensions?**
- Covers security (adversarial inputs), reliability (hallucinations, consistency), scalability (token efficiency), and quality (ambiguity, completeness)
- Each dimension addresses a distinct failure category
- Comprehensive without being overwhelming

**Risk Score Interpretation:**
- **1-3**: Low risk, minor issues only
- **4-6**: Moderate risk, significant improvements needed
- **7-8**: High risk, critical issues present, not production-ready
- **9-10**: Severe risk, fundamental problems, requires major redesign

### Known Limitations
- Cannot test prompts in real deployment environments (only static analysis)
- Effectiveness depends on the analyst's ability to anticipate edge cases
- May miss domain-specific failure modes without context
- Best used as part of a comprehensive testing strategy (not a replacement for live testing)

### Related Prompts
- [Design Prompt with Neura Architect](neura-prompt-creator.md) — Design and optimize prompts
- *Review Code for Production Readiness* (to be created) — Similar forensic approach for code

---

**Version:** 1.0
**Created:** 2026-02-01
**Author:** Prompt Library Contributors
