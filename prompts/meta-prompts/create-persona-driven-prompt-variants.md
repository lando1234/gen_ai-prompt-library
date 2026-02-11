---
type: meta-prompt
complexity: advanced
context: hybrid
output_format: structured
interaction_mode: single-shot
production_ready: experimental
tags: [persona, audience-adaptation, communication, meta-prompting]
---

# Create Persona-Driven Prompt Variants

## Purpose
Generate versions of the same prompt adapted for different audiences (junior dev, senior architect, product manager, executive), adjusting tone, technical depth, and focus.

## Context
Use this meta-prompt when:
- Same information needs to reach multiple stakeholders
- Documentation must serve different skill levels
- Training materials need audience-specific versions
- API/tool docs require technical and business perspectives

---

## Prompt

You are an audience adaptation specialist. Your task is to create persona-specific variants of a prompt that maintain core intent while optimizing for each audience's needs.

**INPUT**: A base prompt and target personas.

**PROCESS**:

1. **Analyze Base Prompt**:
   - Extract core objective and constraints
   - Identify technical concepts and jargon
   - Map which elements are essential vs. adaptable
   - Determine output format requirements

2. **Profile Each Persona**:
   **Junior Developer**:
   - Needs: Step-by-step guidance, examples, explanations
   - Tone: Educational, encouraging, explicit
   - Depth: Explain "why" and "how", avoid assumptions

   **Senior Architect**:
   - Needs: Trade-offs, scalability, integration points
   - Tone: Concise, technical, decision-focused
   - Depth: Architectural implications, patterns, best practices

   **Product Manager**:
   - Needs: User impact, timelines, alternatives
   - Tone: Business-focused, outcome-oriented
   - Depth: High-level technical, emphasize value and trade-offs

   **Executive**:
   - Needs: ROI, risk, strategic alignment
   - Tone: Brief, metrics-driven, actionable
   - Depth: Minimal technical detail, focus on business impact

3. **Adapt Prompt Per Persona**:
   - **Vocabulary**: Adjust technical depth
   - **Structure**: Complexity of instructions
   - **Examples**: Relevant to persona's context
   - **Output**: Format appropriate for use case
   - **Constraints**: Emphasize what matters to persona

**OUTPUT FORMAT**:
```json
{
  "base_prompt_summary": "<core objective in 1 sentence>",
  "persona_analysis": {
    "<persona_name>": {
      "primary_needs": ["need 1", "need 2"],
      "knowledge_level": "beginner|intermediate|advanced|expert",
      "decision_criteria": ["criteria 1", "criteria 2"],
      "communication_preferences": "<tone and style>"
    }
  },
  "variants": [
    {
      "persona": "<persona name>",
      "adapted_prompt": "<full prompt text adapted for this persona>",
      "adaptation_rationale": {
        "vocabulary_changes": "<what was simplified/complexified>",
        "structure_changes": "<how organization changed>",
        "emphasis_shift": "<what was prioritized>",
        "examples_added": "<persona-relevant examples>"
      },
      "usage_context": "<when this variant should be used>"
    }
  ],
  "cross_variant_comparison": {
    "preserved_across_all": ["element 1", "element 2"],
    "biggest_differences": ["difference 1", "difference 2"],
    "validation_notes": "<how to test each variant>"
  }
}
```

**CONSTRAINTS**:
- Core objective must remain identical across variants
- Essential constraints cannot be removed (only re-phrased)
- Output format can vary if appropriate for persona
- Never patronize or oversimplify inappropriately
- Maintain professional tone across all variants

---

## Example

**Input Prompt**:
```
Design a caching strategy for our API to reduce database load.
```

**Output**:
```json
{
  "base_prompt_summary": "Design API caching to reduce database load",
  "persona_analysis": {
    "Junior Developer": {
      "primary_needs": ["What is caching", "Step-by-step implementation", "Debugging help"],
      "knowledge_level": "beginner",
      "decision_criteria": ["Ease of implementation", "Learning opportunity"],
      "communication_preferences": "Educational with examples"
    },
    "Senior Architect": {
      "primary_needs": ["Scalability analysis", "Trade-offs", "Integration patterns"],
      "knowledge_level": "expert",
      "decision_criteria": ["Performance", "Maintainability", "Cost"],
      "communication_preferences": "Concise and technical"
    },
    "Product Manager": {
      "primary_needs": ["User impact", "Timeline", "Cost vs. benefit"],
      "knowledge_level": "intermediate",
      "decision_criteria": ["Time to market", "User experience", "Resource cost"],
      "communication_preferences": "Business outcomes focused"
    },
    "Executive": {
      "primary_needs": ["ROI", "Risk assessment", "Strategic fit"],
      "knowledge_level": "beginner",
      "decision_criteria": ["Financial impact", "Competitive advantage"],
      "communication_preferences": "Brief with metrics"
    }
  },
  "variants": [
    {
      "persona": "Junior Developer",
      "adapted_prompt": "You are a caching mentor teaching API optimization.\n\n**BACKGROUND**: Caching stores frequently accessed data in fast memory (like Redis) so the database isn't hit repeatedly for the same information.\n\n**YOUR TASK**: Design a caching strategy for our API that reduces database queries.\n\n**PROVIDE**:\n1. **Explanation**: What is caching and why it helps (2-3 sentences)\n2. **Implementation steps**: \n   - What to cache (which endpoints/data)\n   - Where to store cache (Redis, in-memory, CDN)\n   - When to invalidate cache\n3. **Code example**: Simple cache wrapper in [language]\n4. **Common pitfalls**: What can go wrong and how to avoid\n5. **Testing approach**: How to verify it's working\n\n**OUTPUT**: Step-by-step guide with code snippets",
      "adaptation_rationale": {
        "vocabulary_changes": "Added explanations for 'caching', 'invalidate', 'Redis'",
        "structure_changes": "Broke down into numbered steps with sub-bullets",
        "emphasis_shift": "Focus on learning and implementation details",
        "examples_added": "Requested code example and common pitfalls"
      },
      "usage_context": "Onboarding, training, or when junior dev is implementing the feature"
    },
    {
      "persona": "Senior Architect",
      "adapted_prompt": "Design a production-grade caching strategy for our API to reduce database load.\n\n**REQUIREMENTS**:\n- Current: 10K req/s, 80% read-heavy, PostgreSQL primary\n- Goal: Reduce DB load by 60%, maintain <100ms p99 latency\n\n**PROVIDE**:\n1. **Architecture**: Cache topology (distributed/local, layers)\n2. **Strategy**: \n   - Caching pattern (cache-aside, write-through, etc.)\n   - TTL strategy and invalidation logic\n   - Consistency model (eventual vs. strong)\n3. **Trade-offs**: \n   - Staleness vs. performance\n   - Memory cost vs. DB savings\n   - Complexity vs. reliability\n4. **Integration**: How it fits with existing infrastructure\n5. **Failure modes**: What happens when cache is down\n\n**OUTPUT**: Technical design doc with diagrams and scalability analysis",
      "adaptation_rationale": {
        "vocabulary_changes": "Uses technical terms (p99 latency, cache-aside, TTL)",
        "structure_changes": "Focuses on architecture and trade-offs",
        "emphasis_shift": "Scalability, reliability, and integration",
        "examples_added": "Quantitative requirements and failure modes"
      },
      "usage_context": "Architecture review, system design, scaling decisions"
    },
    {
      "persona": "Product Manager",
      "adapted_prompt": "Propose a caching strategy to improve API performance and user experience.\n\n**CONTEXT**: Users experience slow load times (2-3s) on dashboard due to database bottleneck.\n\n**PROVIDE**:\n1. **User impact**: How caching improves load times (target: <500ms)\n2. **Approach**: High-level caching strategy (in plain language)\n3. **Timeline**: Implementation phases and estimated effort\n4. **Costs**: \n   - Engineering time (weeks)\n   - Infrastructure cost ($/month)\n   - Maintenance overhead\n5. **Alternatives**: Other options considered (CDN, query optimization)\n6. **Risks**: What could go wrong and mitigation\n\n**OUTPUT**: Product brief with user benefits, timeline, and cost-benefit analysis",
      "adaptation_rationale": {
        "vocabulary_changes": "Simplified technical terms, focus on outcomes",
        "structure_changes": "Emphasizes user impact and timeline",
        "emphasis_shift": "Business value and resource planning",
        "examples_added": "Alternatives and risk mitigation"
      },
      "usage_context": "Feature planning, roadmap prioritization, stakeholder alignment"
    },
    {
      "persona": "Executive",
      "adapted_prompt": "Evaluate implementing caching to reduce infrastructure costs and improve customer experience.\n\n**BUSINESS CASE**:\n- **Problem**: Database bottleneck causing slow user experience\n- **Proposed solution**: Caching layer\n- **Expected ROI**: \n  - Reduce infrastructure costs by 40% ($50K/year)\n  - Improve page load times from 2.5s → 0.4s\n  - Reduce churn risk for 15% of users\n\n**DECISION FRAMEWORK**:\n1. **Financial impact**: Cost savings vs. investment\n2. **Customer value**: User experience improvement\n3. **Competitive position**: Industry standard implementation\n4. **Risk assessment**: Implementation risks and mitigation\n5. **Timeline**: 6-8 weeks to full deployment\n\n**RECOMMENDATION**: Approve/defer with 1-paragraph rationale\n\n**OUTPUT**: Executive summary (max 200 words) with go/no-go recommendation",
      "adaptation_rationale": {
        "vocabulary_changes": "Business metrics replace technical details",
        "structure_changes": "Decision framework instead of implementation",
        "emphasis_shift": "ROI, risk, strategic alignment",
        "examples_added": "Financial impact and competitive positioning"
      },
      "usage_context": "Budget approval, strategic planning, board presentations"
    }
  ],
  "cross_variant_comparison": {
    "preserved_across_all": [
      "Core objective: reduce database load via caching",
      "Need to address approach and trade-offs",
      "Must provide actionable output"
    ],
    "biggest_differences": [
      "Junior: educational depth, Senior: technical depth",
      "PM: timeline/cost focus, Executive: ROI/risk focus",
      "Output format varies by use case"
    ],
    "validation_notes": "Test each variant with target persona - verify comprehension and actionability"
  }
}
```

---

## Usage Notes

- **When to use**: Multi-stakeholder communication, documentation, training
- **When NOT to use**: Single-audience prompts (no adaptation needed)
- **Best practice**: Validate each variant with actual persona representatives
- **Iteration**: Gather feedback from each persona and refine

---

## Quality Checklist

- [ ] Core objective preserved across all variants
- [ ] Vocabulary appropriate for each persona's knowledge level
- [ ] Structure matches persona's decision-making needs
- [ ] Examples relevant to each persona's context
- [ ] Tone professional and non-patronizing
- [ ] Output format suitable for each use case
- [ ] Trade-offs emphasized for decision-makers
- [ ] Implementation details for technical personas
