---
type: meta-prompt
complexity: advanced
context: technical
output_format: structured
interaction_mode: single-shot
production_ready: experimental
tags: [workflows, chains, decomposition, orchestration, meta-prompting]
---

# Create Prompt Chain from Complex Task

## Purpose
Decompose large, complex tasks into a sequence of smaller prompts with clear inputs/outputs between steps and orchestration logic.

## Context
Use this meta-prompt when:
- A single prompt is too complex or overloaded
- Task requires multiple distinct stages
- Intermediate outputs need validation/processing
- Different steps need different expertise/models

---

## Prompt

You are a prompt chain architect. Your task is to decompose complex tasks into manageable prompt sequences with clear data flow and orchestration rules.

**INPUT**: A complex task description that's too large for a single prompt.

**PROCESS**:

1. **Decompose Task**:
   - Identify distinct logical stages
   - Map dependencies between stages
   - Determine which stages can parallelize
   - Identify decision points/branches

2. **Design Each Prompt**:
   - **Role**: What expertise is needed?
   - **Input schema**: What data comes from previous step?
   - **Task**: What specific action is performed?
   - **Output schema**: What data flows to next step?
   - **Validation**: How to verify output quality?

3. **Define Orchestration**:
   - Sequential vs. parallel execution
   - Conditional branching logic
   - Error handling and retries
   - State management between prompts

4. **Create Data Flow**:
   - Input/output contracts
   - Variable passing mechanism
   - Intermediate storage needs
   - Aggregation of parallel results

**OUTPUT FORMAT**:
```json
{
  "original_task": "<complex task description>",
  "decomposition_analysis": {
    "complexity_factors": ["factor 1", "factor 2"],
    "why_chain_needed": "<rationale for splitting>",
    "total_stages": <number>,
    "estimated_improvement": "<expected benefit>"
  },
  "workflow_diagram": {
    "type": "sequential|parallel|branching|hybrid",
    "stages": [
      {
        "id": "stage_1",
        "runs_after": ["stage_id or 'START'"],
        "runs_before": ["stage_id or 'END'"],
        "can_parallelize_with": ["stage_id or null"]
      }
    ]
  },
  "prompt_chain": [
    {
      "stage_id": "<unique id>",
      "stage_name": "<descriptive name>",
      "sequence_order": <number>,
      "role": "<specialized role for this stage>",
      "prompt_text": "<full prompt for this stage>",
      "input_schema": {
        "variable_name": {
          "type": "string|number|array|object",
          "description": "<what this input represents>",
          "source": "<from stage_id or 'USER'>",
          "required": true|false
        }
      },
      "output_schema": {
        "variable_name": {
          "type": "string|number|array|object",
          "description": "<what this output represents>",
          "consumed_by": ["stage_id or 'USER'"],
          "validation_rules": ["rule 1", "rule 2"]
        }
      },
      "validation_criteria": {
        "format_check": "<how to verify output format>",
        "quality_gate": "<when to proceed vs. retry>",
        "max_retries": <number>
      }
    }
  ],
  "orchestration_logic": {
    "execution_strategy": "sequential|parallel|conditional",
    "branching_conditions": [
      {
        "after_stage": "stage_id",
        "condition": "<what determines branch>",
        "if_true": "stage_id to execute",
        "if_false": "stage_id to execute"
      }
    ],
    "parallelization": [
      {
        "stages": ["stage_1", "stage_2"],
        "aggregation": "<how to combine results>"
      }
    ],
    "error_handling": {
      "retry_strategy": "<how to handle failures>",
      "fallback_behavior": "<what to do if all retries fail>",
      "rollback_logic": "<if partial completion needs reversal>"
    },
    "state_management": {
      "storage_mechanism": "memory|file|database",
      "persistence_needs": ["what needs to survive crashes"],
      "cleanup_rules": "<when to purge intermediate data>"
    }
  },
  "implementation_guidance": {
    "recommended_tools": ["tool 1", "tool 2"],
    "performance_notes": "<expected latency, cost>",
    "scalability_considerations": "<how to handle high volume>",
    "testing_approach": "<how to validate the chain>"
  }
}
```

**CONSTRAINTS**:
- Minimum 2 stages, maximum 8 (simplify if more needed)
- Each stage must have clear single responsibility
- Input/output contracts must be explicit
- Validation criteria mandatory for each stage
- Document why chain is better than single prompt

---

## Example

**Input Task**:
```
Create a comprehensive technical blog post from a rough idea: research the topic, outline structure, write content, generate code examples, review for accuracy, format for publication.
```

**Output**:
```json
{
  "original_task": "Create comprehensive technical blog post from rough idea",
  "decomposition_analysis": {
    "complexity_factors": [
      "Multiple expertise domains (research, writing, coding)",
      "Requires validation between steps",
      "Different output formats at each stage",
      "Research must complete before writing"
    ],
    "why_chain_needed": "Single prompt would produce generic output lacking depth. Specialized prompts per stage enable higher quality.",
    "total_stages": 5,
    "estimated_improvement": "60% quality increase, better depth and code accuracy"
  },
  "workflow_diagram": {
    "type": "sequential",
    "stages": [
      {
        "id": "research",
        "runs_after": ["START"],
        "runs_before": ["outline"],
        "can_parallelize_with": null
      },
      {
        "id": "outline",
        "runs_after": ["research"],
        "runs_before": ["content", "examples"],
        "can_parallelize_with": null
      },
      {
        "id": "content",
        "runs_after": ["outline"],
        "runs_before": ["review"],
        "can_parallelize_with": ["examples"]
      },
      {
        "id": "examples",
        "runs_after": ["outline"],
        "runs_before": ["review"],
        "can_parallelize_with": ["content"]
      },
      {
        "id": "review",
        "runs_after": ["content", "examples"],
        "runs_before": ["END"],
        "can_parallelize_with": null
      }
    ]
  },
  "prompt_chain": [
    {
      "stage_id": "research",
      "stage_name": "Topic Research",
      "sequence_order": 1,
      "role": "Technical researcher",
      "prompt_text": "You are a technical researcher. Research the topic: {topic}\n\nPROVIDE:\n1. Key concepts and definitions\n2. Common use cases\n3. Best practices and pitfalls\n4. Recent developments (last 2 years)\n5. Recommended resources\n\nOUTPUT: JSON with structured research findings",
      "input_schema": {
        "topic": {
          "type": "string",
          "description": "Blog post topic (e.g., 'Rust async/await patterns')",
          "source": "USER",
          "required": true
        }
      },
      "output_schema": {
        "research_findings": {
          "type": "object",
          "description": "Structured research data",
          "consumed_by": ["outline", "content"],
          "validation_rules": [
            "Must have at least 3 key concepts",
            "Must include 2+ use cases",
            "Must cite sources"
          ]
        }
      },
      "validation_criteria": {
        "format_check": "Valid JSON with required fields",
        "quality_gate": "At least 3 concepts, 2 use cases, 2 sources",
        "max_retries": 2
      }
    },
    {
      "stage_id": "outline",
      "stage_name": "Structure Outline",
      "sequence_order": 2,
      "role": "Content strategist",
      "prompt_text": "You are a content strategist. Create a blog post outline based on:\n\nTOPIC: {topic}\nRESEARCH: {research_findings}\n\nPROVIDE:\n- Title (compelling, SEO-friendly)\n- Introduction hook\n- 3-5 main sections with key points\n- Code examples placeholders\n- Conclusion summary\n\nOUTPUT: Structured outline with section details",
      "input_schema": {
        "topic": {
          "type": "string",
          "description": "Blog post topic",
          "source": "USER",
          "required": true
        },
        "research_findings": {
          "type": "object",
          "description": "Research from stage 1",
          "source": "research",
          "required": true
        }
      },
      "output_schema": {
        "outline": {
          "type": "object",
          "description": "Blog structure with sections",
          "consumed_by": ["content", "examples"],
          "validation_rules": [
            "Must have title",
            "3-5 main sections",
            "Each section has 2+ key points"
          ]
        }
      },
      "validation_criteria": {
        "format_check": "JSON with title, sections array",
        "quality_gate": "3-5 sections, each with key points",
        "max_retries": 2
      }
    },
    {
      "stage_id": "content",
      "stage_name": "Write Content",
      "sequence_order": 3,
      "role": "Technical writer",
      "prompt_text": "You are a technical writer. Write blog post content:\n\nOUTLINE: {outline}\nRESEARCH: {research_findings}\n\nWrite engaging, accurate content for each section. Use clear explanations, real-world examples. Placeholder [CODE_EXAMPLE_N] where code should go.\n\nOUTPUT: Full blog post text (markdown)",
      "input_schema": {
        "outline": {
          "type": "object",
          "description": "Blog outline from stage 2",
          "source": "outline",
          "required": true
        },
        "research_findings": {
          "type": "object",
          "description": "Research from stage 1",
          "source": "research",
          "required": true
        }
      },
      "output_schema": {
        "content_text": {
          "type": "string",
          "description": "Full blog post markdown",
          "consumed_by": ["review"],
          "validation_rules": [
            "1000-2000 words",
            "Covers all outline sections",
            "Has placeholders for code"
          ]
        }
      },
      "validation_criteria": {
        "format_check": "Valid markdown, 1000+ words",
        "quality_gate": "All sections covered, readable",
        "max_retries": 1
      }
    },
    {
      "stage_id": "examples",
      "stage_name": "Generate Code Examples",
      "sequence_order": 3,
      "role": "Software engineer",
      "prompt_text": "You are a software engineer. Generate code examples for:\n\nOUTLINE: {outline}\nRESEARCH: {research_findings}\n\nFor each section needing code:\n- Write functional, tested code\n- Add inline comments\n- Include setup/usage instructions\n\nOUTPUT: JSON array of code snippets with IDs",
      "input_schema": {
        "outline": {
          "type": "object",
          "description": "Blog outline from stage 2",
          "source": "outline",
          "required": true
        },
        "research_findings": {
          "type": "object",
          "description": "Research from stage 1",
          "source": "research",
          "required": true
        }
      },
      "output_schema": {
        "code_examples": {
          "type": "array",
          "description": "Code snippets with IDs",
          "consumed_by": ["review"],
          "validation_rules": [
            "At least 2 examples",
            "Each has language specified",
            "Includes setup instructions"
          ]
        }
      },
      "validation_criteria": {
        "format_check": "JSON array with id, language, code",
        "quality_gate": "2+ examples, syntactically valid",
        "max_retries": 2
      }
    },
    {
      "stage_id": "review",
      "stage_name": "Review and Finalize",
      "sequence_order": 4,
      "role": "Technical editor",
      "prompt_text": "You are a technical editor. Review and finalize:\n\nCONTENT: {content_text}\nCODE: {code_examples}\n\nTASKS:\n1. Replace [CODE_EXAMPLE_N] with actual code\n2. Check technical accuracy\n3. Fix grammar/style issues\n4. Ensure code-content alignment\n5. Add meta description (155 chars)\n\nOUTPUT: Final markdown ready for publication",
      "input_schema": {
        "content_text": {
          "type": "string",
          "description": "Blog content from stage 3",
          "source": "content",
          "required": true
        },
        "code_examples": {
          "type": "array",
          "description": "Code snippets from stage 3",
          "source": "examples",
          "required": true
        }
      },
      "output_schema": {
        "final_post": {
          "type": "object",
          "description": "Complete publication-ready post",
          "consumed_by": ["USER"],
          "validation_rules": [
            "All placeholders replaced",
            "Has meta description",
            "1000+ words"
          ]
        }
      },
      "validation_criteria": {
        "format_check": "Valid markdown, no placeholders",
        "quality_gate": "All sections, code integrated, meta included",
        "max_retries": 1
      }
    }
  ],
  "orchestration_logic": {
    "execution_strategy": "hybrid",
    "branching_conditions": [],
    "parallelization": [
      {
        "stages": ["content", "examples"],
        "aggregation": "Both outputs merged in 'review' stage"
      }
    ],
    "error_handling": {
      "retry_strategy": "Retry failed stage up to max_retries, then prompt user intervention",
      "fallback_behavior": "If research fails, use generic outline; if content fails, return outline only",
      "rollback_logic": "No rollback needed (stages are additive)"
    },
    "state_management": {
      "storage_mechanism": "memory (JSON state object)",
      "persistence_needs": ["research_findings", "outline", "content_text", "code_examples"],
      "cleanup_rules": "Retain all data until final_post delivered to user"
    }
  },
  "implementation_guidance": {
    "recommended_tools": [
      "LangChain for orchestration",
      "JSON Schema for validation",
      "Retry with exponential backoff"
    ],
    "performance_notes": "Stages 3&4 run parallel (30% time savings). Total: ~2-3 min for typical post.",
    "scalability_considerations": "For bulk generation, queue system with rate limiting per stage",
    "testing_approach": "Test each stage independently with mock data, then E2E with real topics"
  }
}
```

---

## Usage Notes

- **When to use**: Task is too complex for single prompt or requires multiple expertise domains
- **When NOT to use**: Simple tasks that don't benefit from decomposition
- **Best practice**: Test each stage independently before chaining
- **Iteration**: Optimize stage boundaries based on failure patterns

---

## Quality Checklist

- [ ] 2-8 stages with clear single responsibilities
- [ ] Input/output schemas fully specified
- [ ] Validation criteria for each stage
- [ ] Orchestration logic addresses parallelization and errors
- [ ] Data flow diagram shows dependencies
- [ ] Error handling and retry strategy defined
- [ ] Implementation guidance provided
- [ ] Explains why chain is better than single prompt
