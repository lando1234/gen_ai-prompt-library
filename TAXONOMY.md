# Taxonomy and Classification System

This document defines how prompts are organized, classified, and evolved within this library.

---

## Core Philosophy

**Type-first, domain-second**: Organization by intent and function, not by industry or technology.

**Organic growth**: Categories emerge from usage patterns, not upfront planning.

**Multi-dimensional metadata**: Prompts are tagged across multiple axes for flexible navigation.

---

## Primary Categories (Type Dimension)

### 1. Meta-Prompts
Prompts that create, improve, or analyze other prompts.

**Characteristics:**
- Output is a prompt or prompt improvement
- Recursive/self-referential nature
- Requires understanding of prompt engineering principles

**Examples:**
- Generate a domain-specific prompt from requirements
- Improve a prompt for clarity and precision
- Analyze a prompt for failure modes

**Complexity**: Intermediate → Expert

---

### 2. System Design
Architecture, technical design, and system-level thinking prompts.

**Characteristics:**
- Focus on structure, scalability, trade-offs
- Multi-component systems
- Technical decision-making support

**Examples:**
- Design a microservices architecture
- Evaluate database choices for a use case
- Create a system design document from requirements

**Complexity**: Advanced → Expert

---

### 3. Code Generation
Prompts that produce executable code with strong output contracts.

**Characteristics:**
- Output is runnable code
- Explicit language, framework, and style requirements
- Clear success criteria (passes tests, meets spec)

**Examples:**
- Generate a REST API endpoint in FastAPI
- Create unit tests for a given function
- Refactor code for readability

**Complexity**: Basic → Advanced

---

### 4. Analysis
Review, audit, and evaluation prompts.

**Characteristics:**
- Assess quality, correctness, or compliance
- Identify issues, risks, or improvement areas
- Output is structured feedback or report

**Examples:**
- Review code for production readiness
- Audit a system design for security vulnerabilities
- Analyze API design for RESTful best practices

**Complexity**: Intermediate → Expert

---

### 5. Strategic
Product, business, and high-level decision support.

**Characteristics:**
- Focus on outcomes, priorities, and trade-offs
- Less technical, more business/product oriented
- Outputs inform decisions, not implementations

**Examples:**
- Prioritize feature requests by impact
- Draft a product requirements document
- Evaluate build vs. buy for a capability

**Complexity**: Intermediate → Advanced

---

### 6. Workflows
Multi-step, orchestrated processes involving multiple prompts or stages.

**Characteristics:**
- Sequential or branching logic
- Combines multiple prompt types
- Requires state management between steps

**Examples:**
- Full software development lifecycle from idea to deployment
- Incident response playbook
- Research and synthesis pipeline

**Complexity**: Advanced → Expert

---

### 7. Specialized
Domain-specific prompts that don't fit cleanly into the above.

**Subcategories** (expandable):
- `data-engineering/`: ETL, pipelines, data quality
- `infrastructure/`: DevOps, IaC, cloud architecture
- `security/`: AppSec, threat modeling, compliance

**Complexity**: Varies

---

## Secondary Classification Dimensions

Every prompt MUST declare the following metadata in its frontmatter or header:

### Complexity Level
- **Basic**: Straightforward, single-purpose, minimal context required
- **Intermediate**: Multiple variables, some domain knowledge needed
- **Advanced**: Complex scenarios, deep expertise required
- **Expert**: Multi-dimensional, production-critical, high stakes

### Context Type
- **Technical**: Engineering, architecture, code
- **Business**: Product, strategy, operations
- **Hybrid**: Requires both technical and business judgment

### Output Format
- **Structured**: JSON, YAML, tables, lists
- **Free-form**: Prose, narrative, explanatory
- **Code**: Executable source code
- **Markdown**: Documentation, reports, README files

### Interaction Mode
- **Single-shot**: One prompt, one complete response
- **Iterative**: Requires follow-up or refinement
- **Conversational**: Multi-turn dialogue expected

### Production Readiness
- **Yes**: Battle-tested, used in real systems
- **Experimental**: Promising but unvalidated

---

## Naming Conventions

### File Naming
```
<verb>-<object>-<qualifier>.md
```

**Rules:**
- All lowercase
- Hyphens as separators
- Descriptive, not cute
- Max 60 characters

**Examples:**
- `generate-rest-api-from-openapi-spec.md`
- `review-code-for-security-vulnerabilities.md`
- `create-prompt-for-technical-domain.md`
- `design-event-driven-architecture.md`

### Title (within file)
```
# [Verb] [Object] [Context/Qualifier]
```

**Examples:**
- `# Generate REST API Endpoint from OpenAPI Spec`
- `# Review Code for Production Readiness`
- `# Design Event-Driven System Architecture`

---

## Evolution Rules

### When to Create a New Category
A new top-level category should be created when:
1. An existing category exceeds 12-15 prompts
2. A clear sub-pattern emerges with distinct characteristics
3. Users repeatedly ask "where would a [X] prompt go?"

### When to Create a Subcategory
Within an existing category:
1. When 8-10 prompts share a more specific pattern
2. When domain expertise becomes a requirement (e.g., "kubernetes-specific" within infrastructure)

### When to Merge Categories
If a category has <3 prompts after 6 months, consider:
1. Merging into a parent category
2. Reclassifying into an existing category
3. Removing if unused

---

## Metadata Template

Every prompt file should start with:

```markdown
---
type: [meta-prompt | system-design | code-generation | analysis | strategic | workflow | specialized]
complexity: [basic | intermediate | advanced | expert]
context: [technical | business | hybrid]
output_format: [structured | free-form | code | markdown]
interaction_mode: [single-shot | iterative | conversational]
production_ready: [yes | experimental]
tags: [optional, additional, keywords]
---
```

This metadata enables:
- Filtering and search
- Quality tracking
- Usage analytics
- Community contribution validation

---

## Quality Gate

Before a prompt is cataloged, verify:

- [ ] File name follows convention
- [ ] Metadata is complete and accurate
- [ ] Fits clearly into exactly one primary category
- [ ] Complexity level is justified
- [ ] Production readiness is honest

---

**Last Updated**: 2026-02-01
