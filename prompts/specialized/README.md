# Specialized

Domain-specific prompts that require **niche expertise** or don't fit cleanly into other categories.

---

## What Belongs Here

Prompts that are:
- **Domain-specific**: Require specialized knowledge (data engineering, security, DevOps, etc.)
- **Technology-specific**: Tied to a particular tool, framework, or platform
- **Niche use cases**: Not broadly applicable across different contexts

---

## Subcategories

### `data-engineering/`
- ETL and data pipeline design
- Data quality and validation
- Data modeling and schema design
- Big data processing

### `infrastructure/`
- DevOps and CI/CD
- Infrastructure as Code (IaC)
- Cloud architecture (AWS, Azure, GCP)
- Containerization and orchestration (Docker, Kubernetes)

### `security/`
- Application security (AppSec)
- Threat modeling
- Compliance and auditing (GDPR, SOC2, etc.)
- Penetration testing and vulnerability assessment

---

## When to Use This Category

Use `specialized/` when a prompt:
1. Requires domain expertise not expected of general practitioners
2. Is tied to a specific technology or platform
3. Doesn't fit cleanly into `system-design`, `code-generation`, or `analysis`

**However:** If a prompt is domain-specific but fits another category's purpose, prefer that category.

**Example:**
- `generate-terraform-module.md` → Could go in `code-generation/` (generates code) OR `specialized/infrastructure/` (IaC-specific)
- **Decision**: If it's a straightforward code generation pattern, use `code-generation/`. If it requires deep IaC/cloud knowledge, use `specialized/infrastructure/`.

---

## Complexity Range

**Varies** — depends on domain. Generally:
- Basic domain knowledge → Intermediate
- Deep expertise required → Advanced/Expert

---

## Examples of Prompts That Belong Here

- `specialized/data-engineering/design-etl-pipeline.md`
- `specialized/infrastructure/create-kubernetes-deployment.md`
- `specialized/security/perform-threat-model-analysis.md`

---

## Examples of Prompts That DON'T Belong Here

- General architecture design (→ `system-design/`)
- Generic code generation (→ `code-generation/`)
- Non-domain-specific analysis (→ `analysis/`)

**Rule of thumb:** If the prompt requires **specialized domain knowledge** that most engineers don't have, it belongs here.

---

## Subdirectory Expansion

Start with these three subcategories:
- `data-engineering/`
- `infrastructure/`
- `security/`

Add new subcategories as needed when:
- 5+ prompts share a domain
- The domain requires distinct expertise
- The prompts don't fit elsewhere

**Potential future subcategories:**
- `frontend/` — UI/UX-specific prompts
- `mobile/` — iOS/Android-specific
- `machine-learning/` — ML model design, training, evaluation
- `compliance/` — Legal, regulatory, policy

---

## Best Practices

1. **Make domain assumptions explicit**: "Assumes familiarity with Kubernetes concepts"
2. **Define domain-specific jargon**: Don't assume everyone knows "CDC" or "RBAC"
3. **Link to external resources**: "See [Kubernetes docs] for terminology"
4. **Specify technology versions**: "For Terraform 1.5+", "AWS SDK v3"
