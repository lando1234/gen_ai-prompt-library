# GenAI Prompt Library

A production-ready, curated collection of prompts for generative AI systems — designed for practitioners who build real systems, not demos.

---

## Philosophy

This library is built on three principles:

1. **Explicit Contracts**: Every prompt has clear inputs, outputs, and success criteria
2. **Production Mindset**: Prompts are designed for reusability, maintainability, and real-world failure modes
3. **Organic Evolution**: The taxonomy grows with usage, not by upfront theory

This is not a prompt dump. It's a **living engineering artifact** that reflects how senior practitioners think about prompt design, classification, and quality.

---

## Who This Is For

- **AI Engineers** building GenAI-powered features into production systems
- **Engineering Managers** standardizing prompt patterns across teams
- **Prompt Engineers** seeking structured, reusable templates
- **Technical Leaders** evaluating prompt quality and design patterns

If you're looking for "10 ChatGPT hacks," this isn't the place. If you're building systems that need to work reliably at scale, keep reading.

---

## Repository Structure

```
prompts/               # Core library, organized by type and intent
examples/              # Real-world usage demonstrations
templates/             # Reusable scaffolds and standards
docs/                  # Best practices, principles, and guidelines
```

### Prompt Categories

| Category | Description | Complexity Range |
|----------|-------------|------------------|
| **Meta-Prompts** | Prompts that generate or improve other prompts | Intermediate → Expert |
| **System Design** | Architecture, technical design, system-level thinking | Advanced → Expert |
| **Code Generation** | Code-specific prompts with strong output contracts | Basic → Advanced |
| **Analysis** | Review, audit, and evaluation prompts | Intermediate → Expert |
| **Strategic** | Product, business, and high-level decision support | Intermediate → Advanced |
| **Workflows** | Multi-step, orchestrated processes | Advanced → Expert |
| **Specialized** | Domain-specific prompts (data, infra, security, etc.) | Varies |

The taxonomy is intentionally **shallow initially** and grows as patterns emerge.

---

## Prompt Standard

Every prompt follows a strict structure:

```markdown
# Title

## Usage
When, why, and under what constraints this prompt should be used.

## Inputs
Explicit list of required variables or context.

## Outputs
Expected format, guarantees, and success criteria.

## Prompt
<Full prompt block, copy-pasteable>
```

**No exceptions.** This ensures every prompt is:
- Self-documenting
- Auditable
- Immediately usable

See [`templates/prompt-template.md`](templates/prompt-template.md) for the canonical format.

---

## Quality Standards

Prompts in this library are reviewed for:

- **Clarity**: No implicit assumptions or ambiguous instructions
- **Completeness**: All required inputs and outputs explicitly defined
- **Reusability**: Works across contexts, not overfitted to one scenario
- **Production Readiness**: Handles edge cases, failures, and real-world messiness

See [`docs/quality-standards.md`](docs/quality-standards.md) for the full rubric.

---

## How to Use This Library

1. **Browse by type**: Navigate [`prompts/`](prompts/) by the kind of task you need
2. **Check complexity**: Each prompt declares its intended audience level
3. **Copy and adapt**: Prompts are designed to be forked and customized
4. **Contribute back**: If you improve a prompt, consider opening a PR

---

## Contributing

This is an open, evolving project. Contributions are welcome, but quality bar is high.

- Read [`CONTRIBUTING.md`](CONTRIBUTING.md) before submitting
- Use the standard prompt template
- Expect rigorous review focused on clarity, completeness, and production applicability

---

## About This Project

This library is maintained as a **living portfolio project** by an AI Engineering Manager specialized in Generative AI. It reflects:

- Production experience building GenAI systems at scale
- System-level thinking about prompt design and engineering practices
- A commitment to rigor, maintainability, and real-world applicability

If you're hiring for senior GenAI roles, this repository demonstrates:
- Technical judgment and architectural thinking
- Practical prompt engineering at production scale
- Ability to build reusable, maintainable AI artifacts

---

## License

[MIT License](LICENSE) — use freely, attribute generously.

---

## Roadmap

- [ ] Foundational meta-prompts and system design prompts
- [ ] Real-world case studies and annotated examples
- [ ] Automated prompt quality analysis tooling
- [ ] Community contribution pipeline and review process
- [ ] Integration examples with popular LLM frameworks

---

**Last Updated**: 2026-02-01
