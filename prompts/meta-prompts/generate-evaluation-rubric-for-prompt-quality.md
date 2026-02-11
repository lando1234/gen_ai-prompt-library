---
type: meta-prompt
complexity: intermediate
context: technical
output_format: structured
interaction_mode: single-shot
production_ready: experimental
tags: [evaluation, quality-assessment, rubric, scoring, meta-prompting]
---

# Generate Evaluation Rubric for Prompt Quality

## Purpose
Create quantitative metrics for assessing prompts (clarity, specificity, completeness) with 1-5 scoring system and justifications.

## Context
Use this meta-prompt when:
- Establishing quality standards for prompt library
- Comparing multiple prompt candidates objectively
- Auditing existing prompts for improvement
- Training team on prompt quality criteria

---

## Prompt

You are a prompt quality assessor. Your task is to create objective, actionable evaluation rubrics for measuring prompt quality across key dimensions.

**INPUT**: A prompt to evaluate (or general rubric request).

**PROCESS**:

1. **Define Evaluation Dimensions**:
   - **Clarity**: Instructions are unambiguous and easy to understand
   - **Specificity**: Precise requirements, no vague terms
   - **Completeness**: All necessary elements present
   - **Structure**: Well-organized, scannable format
   - **Consistency**: No contradictions or conflicts
   - **Robustness**: Handles edge cases and errors
   - **Efficiency**: Minimal tokens, no redundancy
   - **Actionability**: Output is usable without further clarification

2. **Create Scoring Criteria**:
   For each dimension, define 5 levels:
   - **5 (Excellent)**: Best-in-class, production-ready
   - **4 (Good)**: Minor improvements needed
   - **3 (Acceptable)**: Functional but has issues
   - **2 (Poor)**: Significant problems, needs rework
   - **1 (Failing)**: Unusable, fundamental flaws

3. **Develop Assessment Process**:
   - How to score each dimension objectively
   - Evidence required for each score
   - Common anti-patterns to flag
   - Weighting if dimensions have different importance

4. **Generate Example Ratings**:
   - Show how rubric applies to real prompts
   - Demonstrate scoring justifications
   - Highlight improvement recommendations

**OUTPUT FORMAT**:
```json
{
  "rubric_metadata": {
    "purpose": "<what this rubric evaluates>",
    "scope": "<type of prompts this applies to>",
    "version": "1.0"
  },
  "dimensions": [
    {
      "dimension": "<name>",
      "weight": <0.0-1.0, must sum to 1.0 across all dimensions>,
      "description": "<what this measures>",
      "scoring_criteria": {
        "5": {
          "descriptor": "<what earns a 5>",
          "indicators": ["indicator 1", "indicator 2"],
          "examples": ["example behavior"]
        },
        "4": {
          "descriptor": "<what earns a 4>",
          "indicators": ["indicator 1"],
          "examples": ["example behavior"]
        },
        "3": {
          "descriptor": "<what earns a 3>",
          "indicators": ["indicator 1"],
          "examples": ["example behavior"]
        },
        "2": {
          "descriptor": "<what earns a 2>",
          "indicators": ["indicator 1"],
          "examples": ["example behavior"]
        },
        "1": {
          "descriptor": "<what earns a 1>",
          "indicators": ["indicator 1"],
          "examples": ["example behavior"]
        }
      },
      "assessment_questions": [
        "<question to ask when evaluating this dimension>"
      ]
    }
  ],
  "anti_patterns": [
    {
      "pattern": "<common mistake>",
      "affected_dimensions": ["dimension 1"],
      "how_to_detect": "<what to look for>",
      "fix": "<how to improve>"
    }
  ],
  "scoring_process": {
    "step_1": "<evaluate dimension X>",
    "step_2": "<evaluate dimension Y>",
    "step_3": "<calculate weighted score>",
    "overall_rating": {
      "90-100": "Excellent",
      "75-89": "Good",
      "60-74": "Acceptable",
      "40-59": "Poor",
      "0-39": "Failing"
    }
  },
  "example_evaluation": {
    "prompt_text": "<sample prompt>",
    "dimension_scores": {
      "dimension": {
        "score": <1-5>,
        "justification": "<why this score>",
        "evidence": "<specific examples from prompt>",
        "improvement_suggestions": ["suggestion 1"]
      }
    },
    "weighted_score": <0-100>,
    "overall_rating": "<Excellent|Good|Acceptable|Poor|Failing>",
    "summary": "<overall assessment>",
    "priority_improvements": ["most important fix 1", "fix 2"]
  }
}
```

**CONSTRAINTS**:
- Scoring must be objective (not subjective opinion)
- Each score level must have clear indicators
- Weights must sum to 1.0
- Provide at least 1 example evaluation
- Include at least 5 common anti-patterns

---

## Example

**Input**: Create a rubric for evaluating task-completion prompts

**Output**:
```json
{
  "rubric_metadata": {
    "purpose": "Evaluate quality of task-completion prompts (e.g., code generation, data analysis)",
    "scope": "Prompts that instruct AI to perform specific tasks with deliverables",
    "version": "1.0"
  },
  "dimensions": [
    {
      "dimension": "Clarity",
      "weight": 0.25,
      "description": "Instructions are unambiguous and easy to understand",
      "scoring_criteria": {
        "5": {
          "descriptor": "Crystal clear with no room for misinterpretation",
          "indicators": [
            "Every term is defined or self-evident",
            "No vague qualifiers ('good', 'nice', 'clean')",
            "Examples provided where needed"
          ],
          "examples": [
            "Generate a Python function that calculates factorial using recursion"
          ]
        },
        "4": {
          "descriptor": "Mostly clear with minor ambiguities",
          "indicators": [
            "1-2 terms could be clearer",
            "Intent is obvious from context"
          ],
          "examples": [
            "Write a good factorial function" (what is 'good'?)
          ]
        },
        "3": {
          "descriptor": "Understandable but requires interpretation",
          "indicators": [
            "Multiple possible interpretations",
            "Missing context affects clarity"
          ],
          "examples": [
            "Create a factorial calculator" (function? class? CLI tool?)
          ]
        },
        "2": {
          "descriptor": "Confusing or contradictory instructions",
          "indicators": [
            "Conflicting requirements",
            "Unclear what output is expected"
          ],
          "examples": [
            "Make a factorial thing that's simple but comprehensive"
          ]
        },
        "1": {
          "descriptor": "Incomprehensible or nonsensical",
          "indicators": [
            "Cannot determine what is being requested",
            "Grammatically broken"
          ],
          "examples": [
            "Do math factorial not slow optimize"
          ]
        }
      },
      "assessment_questions": [
        "Can I understand exactly what's being asked without guessing?",
        "Are all terms defined or contextually obvious?",
        "Would 5 people interpret this the same way?"
      ]
    },
    {
      "dimension": "Specificity",
      "weight": 0.20,
      "description": "Precise requirements with measurable criteria",
      "scoring_criteria": {
        "5": {
          "descriptor": "All requirements quantified and verifiable",
          "indicators": [
            "Specific constraints (language, length, format)",
            "Success criteria are measurable",
            "Edge cases addressed"
          ],
          "examples": [
            "Generate Python 3.9+ function, max 20 lines, handles n=0 and n>1000"
          ]
        },
        "4": {
          "descriptor": "Most requirements specific, 1-2 vague areas",
          "indicators": ["Core requirements precise", "Minor details left open"],
          "examples": ["Generate Python function, handle edge cases (which ones?)"]
        },
        "3": {
          "descriptor": "Mix of specific and vague requirements",
          "indicators": ["Some measurable criteria", "Key aspects unspecified"],
          "examples": ["Generate efficient Python function (how efficient?)"]
        },
        "2": {
          "descriptor": "Mostly vague with few specifics",
          "indicators": ["Qualitative terms dominate", "No measurable criteria"],
          "examples": ["Generate a nice function"]
        },
        "1": {
          "descriptor": "Entirely vague or missing requirements",
          "indicators": ["No specifics at all", "Everything is implied"],
          "examples": ["Do the thing"]
        }
      },
      "assessment_questions": [
        "Are requirements measurable/testable?",
        "Are constraints explicitly stated?",
        "Is the output format clearly defined?"
      ]
    },
    {
      "dimension": "Completeness",
      "weight": 0.20,
      "description": "All necessary elements for task completion present",
      "scoring_criteria": {
        "5": {
          "descriptor": "Everything needed is provided",
          "indicators": [
            "Role/context specified",
            "Input and output schemas defined",
            "Constraints and success criteria included",
            "Examples or edge cases listed"
          ],
          "examples": ["Includes role, input format, output format, constraints, examples"]
        },
        "4": {
          "descriptor": "Most elements present, 1 minor omission",
          "indicators": ["Core elements covered", "Nice-to-have missing"],
          "examples": ["Has role, I/O, constraints, but no examples"]
        },
        "3": {
          "descriptor": "Key elements present but significant gaps",
          "indicators": ["Basic task defined", "Missing constraints or format"],
          "examples": ["Task described but output format unspecified"]
        },
        "2": {
          "descriptor": "Multiple critical elements missing",
          "indicators": ["Cannot complete task without assumptions"],
          "examples": ["Task mentioned but no input/output/constraints"]
        },
        "1": {
          "descriptor": "Bare minimum, unusable",
          "indicators": ["Only task name, no details"],
          "examples": ["Generate code"]
        }
      },
      "assessment_questions": [
        "Is there a defined role/context?",
        "Are input and output formats specified?",
        "Are constraints and edge cases covered?"
      ]
    },
    {
      "dimension": "Structure",
      "weight": 0.10,
      "description": "Well-organized and easy to parse",
      "scoring_criteria": {
        "5": {
          "descriptor": "Perfectly organized with clear hierarchy",
          "indicators": ["Sections/bullets used", "Scannable format", "Logical flow"],
          "examples": ["Uses headers, bullets, numbered steps"]
        },
        "4": {
          "descriptor": "Good structure, minor formatting issues",
          "indicators": ["Mostly organized", "1-2 sections could be clearer"],
          "examples": ["Has structure but lacks sub-headings"]
        },
        "3": {
          "descriptor": "Basic structure, hard to scan",
          "indicators": ["Paragraph-heavy", "Limited visual hierarchy"],
          "examples": ["Wall of text with some line breaks"]
        },
        "2": {
          "descriptor": "Poor organization, hard to follow",
          "indicators": ["Random order", "No visual breaks"],
          "examples": ["Run-on sentences mixing unrelated concepts"]
        },
        "1": {
          "descriptor": "Chaotic or unstructured",
          "indicators": ["No organization", "Stream of consciousness"],
          "examples": ["All one paragraph, no punctuation"]
        }
      },
      "assessment_questions": [
        "Can I quickly find key information?",
        "Is there a clear hierarchy (headers, bullets)?",
        "Does information flow logically?"
      ]
    },
    {
      "dimension": "Robustness",
      "weight": 0.15,
      "description": "Handles edge cases and errors gracefully",
      "scoring_criteria": {
        "5": {
          "descriptor": "Addresses all edge cases and failure modes",
          "indicators": [
            "Edge cases explicitly listed",
            "Error handling specified",
            "Fallback behavior defined"
          ],
          "examples": ["Handle n=0, n<0, n>max_int, non-integer input"]
        },
        "4": {
          "descriptor": "Covers main edge cases",
          "indicators": ["Common cases addressed", "Minor scenarios omitted"],
          "examples": ["Handle n=0 and negatives"]
        },
        "3": {
          "descriptor": "Mentions edge cases but lacks detail",
          "indicators": ["Generic 'handle errors' statement"],
          "examples": ["Handle invalid input"]
        },
        "2": {
          "descriptor": "Edge cases ignored or assumed",
          "indicators": ["Happy path only", "No error handling"],
          "examples": ["Assumes valid positive integer"]
        },
        "1": {
          "descriptor": "No consideration of edge cases",
          "indicators": ["Only describes ideal scenario"],
          "examples": ["Calculate factorial for n"]
        }
      },
      "assessment_questions": [
        "Are edge cases identified?",
        "Is error handling specified?",
        "What happens with invalid input?"
      ]
    },
    {
      "dimension": "Efficiency",
      "weight": 0.10,
      "description": "Minimal tokens without sacrificing clarity",
      "scoring_criteria": {
        "5": {
          "descriptor": "Concise with zero redundancy",
          "indicators": ["Every word serves purpose", "Structured over prose"],
          "examples": ["Role + task + I/O + constraints in 50 tokens"]
        },
        "4": {
          "descriptor": "Mostly efficient, minor redundancy",
          "indicators": ["1-2 unnecessary phrases"],
          "examples": ["Includes 'please' and 'carefully' (filler)"]
        },
        "3": {
          "descriptor": "Acceptable efficiency, some bloat",
          "indicators": ["Verbose explanations", "Repetition of concepts"],
          "examples": ["Explains same constraint twice in different words"]
        },
        "2": {
          "descriptor": "Inefficient, significant redundancy",
          "indicators": ["Long prose where bullets work", "Repetitive"],
          "examples": ["Multi-paragraph explanation for simple task"]
        },
        "1": {
          "descriptor": "Extremely bloated or rambling",
          "indicators": ["Token count 3x+ necessary"],
          "examples": ["Essay-length prompt for trivial task"]
        }
      },
      "assessment_questions": [
        "Can I remove words without losing meaning?",
        "Is prose used where structure would work?",
        "Are concepts repeated unnecessarily?"
      ]
    }
  ],
  "anti_patterns": [
    {
      "pattern": "Vague qualifiers ('good', 'clean', 'nice')",
      "affected_dimensions": ["Clarity", "Specificity"],
      "how_to_detect": "Search for subjective adjectives",
      "fix": "Replace with measurable criteria (e.g., 'under 100 lines', 'follows PEP 8')"
    },
    {
      "pattern": "Missing output format specification",
      "affected_dimensions": ["Completeness", "Clarity"],
      "how_to_detect": "No mention of desired output structure",
      "fix": "Add explicit OUTPUT section with format/schema"
    },
    {
      "pattern": "Conflicting requirements",
      "affected_dimensions": ["Clarity", "Consistency"],
      "how_to_detect": "'simple' AND 'comprehensive' in same prompt",
      "fix": "Clarify priorities or remove contradiction"
    },
    {
      "pattern": "No edge case handling",
      "affected_dimensions": ["Robustness", "Completeness"],
      "how_to_detect": "Only happy path described",
      "fix": "Add section: 'Edge cases to handle: [list]'"
    },
    {
      "pattern": "Walls of text (no structure)",
      "affected_dimensions": ["Structure", "Efficiency"],
      "how_to_detect": "No bullets, headers, or line breaks",
      "fix": "Convert to bullets/sections with clear hierarchy"
    }
  ],
  "scoring_process": {
    "step_1": "Evaluate each dimension independently (1-5)",
    "step_2": "Justify each score with evidence from prompt",
    "step_3": "Calculate: (Clarity×0.25) + (Specificity×0.20) + (Completeness×0.20) + (Structure×0.10) + (Robustness×0.15) + (Efficiency×0.10) × 20",
    "step_4": "Map to overall rating using scale below",
    "overall_rating": {
      "90-100": "Excellent - Production ready",
      "75-89": "Good - Minor tweaks needed",
      "60-74": "Acceptable - Functional but improvable",
      "40-59": "Poor - Significant rework required",
      "0-39": "Failing - Fundamentally flawed"
    }
  },
  "example_evaluation": {
    "prompt_text": "Write a Python function to calculate factorials. Make it efficient.",
    "dimension_scores": {
      "Clarity": {
        "score": 3,
        "justification": "Task is clear (calculate factorials) but 'efficient' is vague",
        "evidence": "'efficient' is subjective, no definition provided",
        "improvement_suggestions": [
          "Specify efficiency metric (time complexity, space complexity)",
          "Add example: 'O(n) time complexity or better'"
        ]
      },
      "Specificity": {
        "score": 2,
        "justification": "Language specified (Python) but many details missing",
        "evidence": "No mention of input range, recursion vs iteration, return type",
        "improvement_suggestions": [
          "Specify input constraints (n >= 0, max value)",
          "Define 'efficient' quantitatively"
        ]
      },
      "Completeness": {
        "score": 2,
        "justification": "Basic task stated but missing critical elements",
        "evidence": "No output format, edge cases, examples, or constraints",
        "improvement_suggestions": [
          "Add input/output examples",
          "Specify edge case handling (n=0, negatives)",
          "Define function signature"
        ]
      },
      "Structure": {
        "score": 3,
        "justification": "Simple sentence, no structure needed for this length",
        "evidence": "One sentence, appropriate for simple request",
        "improvement_suggestions": [
          "If adding details, use bullets for readability"
        ]
      },
      "Robustness": {
        "score": 1,
        "justification": "No edge case or error handling mentioned",
        "evidence": "No mention of n=0, negatives, large n, non-integers",
        "improvement_suggestions": [
          "List edge cases: 'Handle n=0, n<0, n>1000'",
          "Specify error behavior"
        ]
      },
      "Efficiency": {
        "score": 4,
        "justification": "Concise, no unnecessary words",
        "evidence": "15 tokens, direct request",
        "improvement_suggestions": [
          "Good token efficiency maintained"
        ]
      }
    },
    "weighted_score": 47,
    "overall_rating": "Poor",
    "summary": "Basic task definition lacking specificity and robustness. Needs explicit constraints, edge cases, and quantifiable 'efficiency' criteria.",
    "priority_improvements": [
      "1. Define 'efficient' with measurable criteria",
      "2. Add edge case handling (n=0, negatives)",
      "3. Specify input/output format and examples"
    ]
  }
}
```

---

## Usage Notes

- **When to use**: Establishing standards, comparing prompts, quality audits
- **When NOT to use**: For creative/exploratory prompts where quality is subjective
- **Best practice**: Have 2-3 evaluators score independently, then compare
- **Iteration**: Refine rubric based on inter-rater reliability issues

---

## Quality Checklist

- [ ] 5-8 dimensions with clear definitions
- [ ] Weights sum to 1.0
- [ ] Each score (1-5) has specific indicators
- [ ] At least 5 anti-patterns identified
- [ ] Scoring process is objective and repeatable
- [ ] Example evaluation demonstrates usage
- [ ] Assessment questions help guide evaluation
- [ ] Overall rating scale maps to actionable categories
