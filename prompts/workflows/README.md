# Workflows

Prompts for **multi-step, orchestrated processes** involving multiple stages or prompts.

---

## What Belongs Here

- Multi-stage processes that combine multiple prompt types
- Sequential or conditional workflows
- End-to-end processes (e.g., full SDLC, incident response)
- Orchestrations requiring state management between steps
- Complex task breakdowns with dependencies

---

## Characteristics

- **Multi-step execution**: Not a single prompt-response
- **Sequential or branching logic**: Step 2 depends on Step 1 output
- **Combines multiple capabilities**: Code generation + analysis + documentation
- **State management**: Outputs from earlier steps feed into later steps

---

## Complexity Range

**Advanced → Expert**

Workflow prompts require:
- Understanding of complex processes
- Ability to break down large tasks
- Knowledge of dependencies and sequencing
- Experience orchestrating multi-step systems

---

## Examples of Prompts That Belong Here

- `full-feature-development-workflow.md` (requirements → design → code → tests → docs)
- `incident-response-playbook.md` (detect → analyze → mitigate → document)
- `refactoring-workflow.md` (analyze → plan → refactor → test → review)
- `research-and-synthesis-pipeline.md` (gather → analyze → synthesize → report)

---

## Examples of Prompts That DON'T Belong Here

- Single-step prompts (→ appropriate single category)
- Simple sequential tasks that don't require orchestration
- Prompts that can be executed in one go

**Rule of thumb:** If it requires **multiple distinct prompts executed in sequence or with conditional logic**, it belongs here.

---

## Best Practices for Workflow Prompts

1. **Define each step explicitly**: What prompt is used, what inputs it needs
2. **Show dependencies**: "Step 3 requires output from Step 2"
3. **Include decision points**: "If X, go to Step 4A; otherwise Step 4B"
4. **Specify state management**: How to pass outputs between steps
5. **Provide complete example**: End-to-end walkthrough with sample data

---

## Prompts in This Category

| Prompt | Complexity | Description | Production Ready |
|--------|------------|-------------|------------------|
| [Guide PRD Creation with Adaptive Workflow](guide-prd-creation-adaptive.md) | Advanced | Multi-phase PRD creation with adaptive LITE/FULL classification, UX/technical facilitation, and quality gates | Yes |
| [Guide PRD Creation (Quick Reference)](guide-prd-creation-quick-reference.md) | Intermediate | Condensed version of adaptive PRD workflow for experienced PMs (~70% token reduction) | Yes |
| [Setup Team Management Center](setup-team-management-center.md) | Expert | Claude Code skills kit that bootstraps a complete management center (initiatives, quarterly planning, team capacity, executive updates) from 5 parameters | Yes |

---

## Workflow Prompt Structure

Workflow prompts should include:

```markdown
## Workflow Steps

### Step 1: [Name]
- **Prompt**: Link to or inline the prompt
- **Inputs**: What this step needs
- **Outputs**: What this step produces
- **Success Criteria**: How to know it worked

### Step 2: [Name]
- **Depends On**: Step 1
- **Prompt**: ...
- **Inputs**: Outputs from Step 1 + [additional inputs]
- **Outputs**: ...
- **Decision Point**: If [condition], go to Step 3A; else Step 3B

[Continue for all steps]

## Example Execution
[Full walkthrough with sample data]
```
