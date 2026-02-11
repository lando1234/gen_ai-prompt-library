---
type: meta-prompt
complexity: advanced
context: technical
output_format: structured
interaction_mode: single-shot
production_ready: experimental
tags: [reverse-engineering, prompt-discovery, learning, meta-prompting]
---

# Reverse Engineer Prompt from Output

## Purpose
Infer the probable prompt that generated a given LLM response, useful for learning from successful outputs or reproducing results.

## Context
Use this meta-prompt when:
- Analyzing competitors' AI implementations
- Learning from high-quality outputs (how was this achieved?)
- Reproducing results without access to original prompt
- Understanding prompt patterns that work well

---

## Prompt

You are a prompt reverse-engineering specialist. Your task is to analyze LLM outputs and infer the probable prompts that generated them.

**INPUT**: An LLM-generated output (text, code, structured data).

**PROCESS**:

1. **Analyze Output Characteristics**:
   - **Format**: Structure, schema, formatting patterns
   - **Tone**: Formal, casual, technical, creative
   - **Content**: Subject matter, depth, specificity
   - **Style**: Brevity, verbosity, use of examples
   - **Constraints**: What rules seem to be followed?
   - **Patterns**: Repetitive structures, templates

2. **Identify Prompt Elements**:
   - **Role**: What expertise was assumed? (e.g., "technical writer")
   - **Task**: What was the core instruction?
   - **Constraints**: What limitations were imposed?
   - **Output format**: What structure was requested?
   - **Examples**: Were few-shot examples likely provided?
   - **Tone directives**: Any style guidance?

3. **Reconstruct Prompt**:
   - Start with role definition
   - Add task description
   - Include format specifications
   - Add constraints and guardrails
   - Consider few-shot examples if complex

4. **Assess Confidence**:
   - High confidence: Unique patterns strongly suggest specific prompt
   - Medium: Multiple possible prompts could produce this
   - Low: Output is generic, many prompts possible

**OUTPUT FORMAT**:
```json
{
  "output_analysis": {
    "format": "<structure observed>",
    "tone": "<detected tone>",
    "complexity": "basic|intermediate|advanced",
    "distinctive_features": ["feature 1", "feature 2"],
    "constraints_detected": ["constraint 1", "constraint 2"]
  },
  "prompt_elements_inferred": {
    "role": "<probable role specified>",
    "task": "<core instruction>",
    "input_context": "<what input was likely provided>",
    "output_format": "<format instructions>",
    "constraints": ["constraint 1", "constraint 2"],
    "tone_directives": ["directive 1"],
    "few_shot_likely": true|false,
    "reasoning": "<why these elements were inferred>"
  },
  "reconstructed_prompt": "<full prompt text that likely generated this output>",
  "confidence_assessment": {
    "overall_confidence": "high|medium|low",
    "confidence_factors": {
      "format_specificity": "high|medium|low - <why>",
      "unique_patterns": "high|medium|low - <why>",
      "constraint_signals": "high|medium|low - <why>"
    },
    "ambiguities": [
      "<aspect that's unclear or could have multiple interpretations>"
    ]
  },
  "alternative_prompts": [
    {
      "variant": "<alternative prompt that could produce similar output>",
      "likelihood": "high|medium|low",
      "key_differences": "<how this differs from primary reconstruction>"
    }
  ],
  "validation_approach": {
    "test_inputs": ["<inputs to test reconstructed prompt>"],
    "expected_similarity": "<what to compare in outputs>",
    "confidence_thresholds": "<when to consider validation successful>"
  }
}
```

**CONSTRAINTS**:
- Base inference only on observable output characteristics
- Don't invent details not evident in output
- Provide multiple alternatives if ambiguous
- Assess confidence honestly (don't overstate certainty)
- Suggest validation approach to verify reconstruction

---

## Example

**Input Output**:
```json
{
  "summary": "The article discusses three key benefits of serverless architecture: reduced operational overhead, automatic scaling, and pay-per-use pricing. It's ideal for event-driven workloads but may have cold start latency.",
  "key_points": [
    "Reduced ops overhead",
    "Auto-scaling",
    "Cost efficiency"
  ],
  "limitations": [
    "Cold start latency"
  ],
  "word_count": 47
}
```

**Output**:
```json
{
  "output_analysis": {
    "format": "Structured JSON with summary, key_points array, limitations array, word_count",
    "tone": "Technical but accessible, neutral",
    "complexity": "intermediate",
    "distinctive_features": [
      "Exactly 3 key points",
      "Separate limitations section",
      "Word count metadata",
      "Concise summary (one sentence)"
    ],
    "constraints_detected": [
      "JSON output format",
      "Key points as array (not prose)",
      "Word count tracking",
      "Limitation identification required"
    ]
  },
  "prompt_elements_inferred": {
    "role": "Technical summarizer or content analyst",
    "task": "Summarize article about serverless architecture with structured output",
    "input_context": "An article about serverless architecture (text provided to LLM)",
    "output_format": "JSON with summary, key_points, limitations, word_count fields",
    "constraints": [
      "Identify exactly 3 key benefits",
      "Separate limitations",
      "Keep summary to 1-2 sentences",
      "Include word count"
    ],
    "tone_directives": [
      "Be concise and technical"
    ],
    "few_shot_likely": false,
    "reasoning": "Specific JSON schema and structured extraction suggest explicit output format instructions. The 'exactly 3 points' pattern indicates a constraint. Word count tracking is unusual and must have been requested."
  },
  "reconstructed_prompt": "You are a technical content analyst. Summarize the following article about serverless architecture.\n\n**ARTICLE**: [article text]\n\n**TASK**:\n1. Write a 1-2 sentence summary\n2. Extract exactly 3 key benefits\n3. Identify limitations mentioned\n4. Count words in your summary\n\n**OUTPUT FORMAT**:\n```json\n{\n  \"summary\": \"<1-2 sentence summary>\",\n  \"key_points\": [\"<benefit 1>\", \"<benefit 2>\", \"<benefit 3>\"],\n  \"limitations\": [\"<limitation 1>\", ...],\n  \"word_count\": <number of words in summary>\n}\n```",
  "confidence_assessment": {
    "overall_confidence": "high",
    "confidence_factors": {
      "format_specificity": "high - Very specific JSON schema with unusual word_count field strongly suggests explicit format instructions",
      "unique_patterns": "high - Exactly 3 key points (not 2 or 4) indicates explicit constraint",
      "constraint_signals": "medium - Separation of benefits and limitations is common but not universal"
    },
    "ambiguities": [
      "Whether the '3 key points' was explicitly requested or LLM chose to extract 3",
      "Exact phrasing of role ('analyst' vs 'summarizer' vs other)",
      "Whether examples were provided (unlikely given output simplicity)"
    ]
  },
  "alternative_prompts": [
    {
      "variant": "Simpler version without explicit role: 'Summarize this article as JSON with summary, 3 key points, limitations, and word count.'",
      "likelihood": "medium",
      "key_differences": "No role specified, terser instructions, same output format"
    },
    {
      "variant": "Template-based: 'Fill out this template...' with JSON structure pre-defined",
      "likelihood": "medium",
      "key_differences": "Provides schema as template to fill rather than format to follow"
    },
    {
      "variant": "With few-shot example showing another article's structured summary",
      "likelihood": "low",
      "key_differences": "Would likely produce more consistent phrasing across summaries"
    }
  ],
  "validation_approach": {
    "test_inputs": [
      "Another article about serverless with different content",
      "Article about different tech topic (e.g., containers) to test generalization",
      "Article mentioning 5+ benefits to see if exactly 3 are extracted"
    ],
    "expected_similarity": [
      "Same JSON structure",
      "Exactly 3 key points extracted",
      "Limitations in separate array",
      "Word count present and accurate",
      "Similar tone (technical, concise)"
    ],
    "confidence_thresholds": "If 2/3 test outputs match structure and constraints, confidence is validated. If word counts are consistently accurate, format instructions were likely explicit."
  }
}
```

---

## Usage Notes

- **When to use**: Learning from successful outputs, competitive analysis, reproducing results
- **When NOT to use**: When original prompt is available or output is trivial
- **Best practice**: Test reconstructed prompt to validate inference
- **Iteration**: Refine based on validation results

---

## Quality Checklist

- [ ] Output characteristics thoroughly analyzed
- [ ] All prompt elements (role, task, constraints, format) inferred
- [ ] Reconstructed prompt is complete and testable
- [ ] Confidence assessment is honest with specific factors
- [ ] Ambiguities acknowledged
- [ ] At least 2 alternative prompts considered
- [ ] Validation approach provided with test inputs
- [ ] Reasoning explains inference logic
