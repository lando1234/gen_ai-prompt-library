# Contributing Guidelines

Thank you for considering contributing to this library. The quality bar is intentionally high — this is not a prompt collection, it's a curated engineering artifact.

---

## Before You Contribute

Ask yourself:

1. **Is this prompt production-ready?** Has it been used in real systems, not just demos?
2. **Is it reusable?** Does it work across contexts, or is it overfitted to one scenario?
3. **Is it clear?** Can someone unfamiliar with your use case understand and use it?
4. **Does it follow the standard?** See [templates/prompt-template.md](templates/prompt-template.md)

If you answered "no" to any of these, refine before submitting.

---

## Contribution Process

### 1. Check Existing Prompts
Before creating a new prompt:
- Search the library for similar prompts
- Check if an existing prompt can be adapted
- Avoid duplication — better to improve than duplicate

### 2. Use the Template
All prompts MUST follow the standard structure in [templates/prompt-template.md](templates/prompt-template.md):

- **Title**: Clear, verb-driven
- **Usage**: When, why, and under what constraints
- **Inputs**: Explicit, no assumptions
- **Outputs**: Format, guarantees, success criteria
- **Prompt**: Full, copy-pasteable block

### 3. Add Metadata
At the top of your prompt file:

```markdown
---
type: [category]
complexity: [level]
context: [technical | business | hybrid]
output_format: [structured | free-form | code | markdown]
interaction_mode: [single-shot | iterative | conversational]
production_ready: [yes | experimental]
tags: [optional, keywords]
---
```

See [TAXONOMY.md](TAXONOMY.md) for full classification rules.

### 4. Name Your File Correctly
```
<verb>-<object>-<qualifier>.md
```

Examples:
- `generate-api-design-from-requirements.md`
- `review-code-for-production-readiness.md`
- `create-system-architecture-document.md`

### 5. Place in the Right Category
```
prompts/
  ├── meta-prompts/
  ├── system-design/
  ├── code-generation/
  ├── analysis/
  ├── strategic/
  ├── workflows/
  └── specialized/
      ├── data-engineering/
      ├── infrastructure/
      └── security/
```

If unsure, propose a category in your PR description.

### 6. Submit a Pull Request
- One prompt per PR (unless tightly related)
- Clear PR title: `Add: [prompt title]` or `Improve: [prompt title]`
- In the description:
  - Explain the use case
  - Describe why it's production-ready
  - Note any edge cases or limitations

---

## Review Criteria

Your submission will be evaluated on:

### 1. Clarity
- Are inputs and outputs explicit?
- Can someone unfamiliar with the context use it successfully?
- Are there ambiguous terms or implicit assumptions?

### 2. Completeness
- Are all required inputs listed?
- Is the expected output format clear?
- Are success criteria defined?

### 3. Reusability
- Does it work across different contexts?
- Is it overfitted to a single scenario?
- Can it be adapted easily?

### 4. Production Readiness
- Has it been tested in real usage?
- Does it handle edge cases?
- Are failure modes considered?

### 5. Adherence to Standards
- Does it follow the template?
- Is metadata complete?
- Is naming correct?

---

## What Gets Rejected

Examples of submissions that will not be merged:

- **Generic prompts**: "Write me a Python script" — too vague, no contract
- **Overfitted prompts**: Hardcoded to one company, product, or dataset
- **Incomplete prompts**: Missing inputs, outputs, or usage context
- **Marketing fluff**: "Amazing ChatGPT prompt for X" — not engineering-grade
- **Duplicates**: Already covered by an existing prompt
- **Non-standard format**: Doesn't follow the template

---

## Improving Existing Prompts

Improvements to existing prompts are highly valued:

- **Clarity improvements**: Better wording, removed ambiguity
- **Completeness fixes**: Added missing inputs/outputs
- **Edge case handling**: Improved robustness
- **Real-world refinements**: Based on production usage

For improvements:
- PR title: `Improve: [prompt title]`
- Explain what was unclear or incomplete before
- Describe how your change improves it

---

## Code of Conduct

This is a technical project focused on quality and rigor:

- **Be direct**: Clear, constructive feedback is expected
- **Be professional**: Critique the work, not the person
- **Be evidence-based**: "This failed in production" > "I don't like this"
- **Be respectful**: Disagreement is fine, disrespect is not

---

## Questions?

Open an issue with the label `question` or `discussion`.

---

## License

By contributing, you agree that your contributions will be licensed under the same [MIT License](LICENSE) as the project.

---

**Thank you for helping build a rigorous, production-grade prompt library.**
