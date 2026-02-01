# Analysis

Prompts for **review, audit, and evaluation** of code, systems, or artifacts.

---

## What Belongs Here

- Code review prompts
- Security audits
- Performance analysis
- System architecture review
- API design evaluation
- Best practices compliance checks
- Quality and maintainability assessments

---

## Characteristics

- **Assess, don't create**: Reviewing existing artifacts, not generating new ones
- **Structured feedback**: Clear findings, severity, recommendations
- **Criteria-based evaluation**: Explicit standards or best practices
- **Actionable output**: Issues with suggested fixes, not just opinions

---

## Complexity Range

**Intermediate → Expert**

Analysis prompts require:
- Domain expertise (code, architecture, security, etc.)
- Knowledge of best practices and standards
- Ability to identify subtle issues
- Experience with production systems

---

## Examples of Prompts That Belong Here

- `review-code-for-production-readiness.md`
- `audit-api-for-security-vulnerabilities.md`
- `analyze-system-architecture-for-scalability.md`
- `evaluate-database-schema-for-normalization.md`
- `review-pull-request-for-quality.md`

---

## Examples of Prompts That DON'T Belong Here

- Generating code (→ `code-generation/`)
- Designing architecture (→ `system-design/`)
- Creating prompts (→ `meta-prompts/`)
- Product decisions (→ `strategic/`)

**Rule of thumb:** If the output is a review, report, or assessment of something existing, it belongs here.

---

## Best Practices for Analysis Prompts

1. **Define evaluation criteria explicitly**: What makes good code? Secure architecture?
2. **Structure findings**: Use severity levels (critical/high/medium/low)
3. **Include remediation**: Not just "this is wrong", but "here's how to fix it"
4. **Provide examples**: Show what good/bad looks like
5. **Set scope boundaries**: What to review, what to ignore
