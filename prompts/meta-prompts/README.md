# Meta-Prompts

Prompts that **create, improve, or analyze other prompts**.

---

## What Belongs Here

- Prompts that generate new prompts from requirements
- Prompts that refine or improve existing prompts
- Prompts that analyze prompt quality or effectiveness
- Prompts that convert between prompt formats or styles

---

## Characteristics

- **Output is a prompt**: The result is itself a prompt to be used with an LLM
- **Recursive nature**: Meta-level reasoning about prompts
- **Requires prompt engineering knowledge**: User should understand prompt design principles

---

## Complexity Range

**Intermediate → Expert**

Meta-prompts require understanding of:
- Prompt engineering best practices
- LLM behavior and limitations
- Output contract design
- Edge case handling

---

## Examples of Prompts That Belong Here

- `generate-domain-specific-prompt.md`
- `improve-prompt-clarity.md`
- `convert-instruction-to-structured-prompt.md`
- `analyze-prompt-for-failure-modes.md`

---

## Examples of Prompts That DON'T Belong Here

- Prompts that generate code (→ `code-generation/`)
- Prompts that design systems (→ `system-design/`)
- Prompts that analyze code or systems (→ `analysis/`)

**Rule of thumb:** If the output is NOT a prompt, it doesn't belong here.

---

## Available Prompts

| Prompt | Complexity | Description | Production Ready |
|--------|------------|-------------|------------------|
| [Design Prompt with Neura Architect](neura-prompt-creator.md) | Advanced | Multi-mode prompt architect (Fast/Educational/Blueprint) for designing, auditing, and optimizing prompts. Includes full and token-efficient versions. | Yes |
| [Analyze Prompt for Failure Modes](analyze-prompt-for-failure-modes.md) | Advanced | Forensic audit tool for identifying production failure modes in prompts (security, edge cases, hallucinations, consistency). Combines security, QA, and reliability engineering perspectives. | Yes |
