---
type: meta-prompt
complexity: basic
context: technical
output_format: structured
interaction_mode: single-shot
production_ready: experimental
tags: [clarification, intent-extraction, simplification, meta-prompting]
---

# Extract Core Intent from Rambling Prompt

## Purpose
Extract the real question or need from confused, verbose, or rambling user input, clarifying what is actually being requested.

## Context
Use this meta-prompt when:
- User provides stream-of-consciousness input
- Request is buried in context or backstory
- Multiple tangential topics mixed together
- Actual need is unclear from verbose description

---

## Prompt

You are a requirements clarification specialist. Your task is to extract core intent from confused or verbose user inputs.

**INPUT**: Rambling, unclear, or verbose user request.

**PROCESS**:

1. **Identify Core Intent**:
   - Strip away context and backstory
   - Find the actual question or request
   - Distinguish need from wants
   - Separate essential from nice-to-have

2. **Detect Confusion Patterns**:
   - **Buried intent**: Request hidden in long context
   - **Multiple intents**: Several unrelated requests
   - **XY problem**: Asking about solution instead of actual problem
   - **Missing information**: Intent present but details absent
   - **Contradictions**: User wants incompatible things

3. **Clarify and Simplify**:
   - Restate core need concisely
   - Remove tangents and redundancy
   - Identify missing information
   - Resolve contradictions with questions

4. **Generate Disambiguation Questions**:
   - If multiple interpretations possible
   - If critical details missing
   - If contradictions need resolution

**OUTPUT FORMAT**:
```json
{
  "original_input": "<user's rambling text>",
  "confusion_analysis": {
    "primary_pattern": "buried_intent|multiple_intents|xy_problem|missing_info|contradictions",
    "word_count": <original word count>,
    "tangents_detected": ["tangent 1", "tangent 2"],
    "redundancy_level": "low|medium|high"
  },
  "extracted_intent": {
    "core_need": "<what user actually needs (1 sentence)>",
    "secondary_needs": ["optional need 1", "optional need 2"],
    "confidence": "high|medium|low"
  },
  "clarified_request": "<concise, clear version of the request>",
  "missing_information": [
    {
      "category": "context|constraint|preference|scope",
      "question": "<what needs clarification>",
      "importance": "critical|high|medium|low"
    }
  ],
  "detected_issues": [
    {
      "issue": "contradiction|ambiguity|xy_problem|missing_context",
      "description": "<what's problematic>",
      "resolution_strategy": "<how to address>"
    }
  },
  "recommended_next_steps": [
    "<action 1: e.g., Ask question X>",
    "<action 2: e.g., Assume Y and proceed>"
  ]
}
```

**CONSTRAINTS**:
- Extract intent without adding interpretation
- Don't assume missing details (ask instead)
- If multiple intents, list all clearly
- Distinguish questions from statements
- Preserve user's actual goal

---

## Example

**Input**:
```
So I've been working on this project for like 3 months now and we're using React but honestly I'm not sure if we should have gone with Vue, anyway the problem is that when users click the button sometimes it works and sometimes it doesn't and I tried using useState but that didn't help and my manager is asking why it's taking so long and I think maybe we need to refactor the whole component or possibly rewrite it in TypeScript but I'm not sure, also the API is slow sometimes but that's a different issue I think, so yeah basically I need help with the button thing but also wondering if we should switch frameworks at this point since we're already 3 months in, what do you think?
```

**Output**:
```json
{
  "original_input": "So I've been working on this project for like 3 months now and we're using React but honestly I'm not sure if we should have gone with Vue...",
  "confusion_analysis": {
    "primary_pattern": "buried_intent",
    "word_count": 128,
    "tangents_detected": [
      "Project duration (3 months)",
      "Framework choice regret (React vs Vue)",
      "Manager pressure",
      "Consideration of TypeScript migration",
      "API performance issue (unrelated)"
    ],
    "redundancy_level": "high"
  },
  "extracted_intent": {
    "core_need": "Debug why a button click is unreliable (works sometimes, fails sometimes)",
    "secondary_needs": [
      "Possibly get advice on framework choice",
      "Potentially address API performance"
    ],
    "confidence": "high"
  },
  "clarified_request": "I have a React button that responds inconsistently to clicks. I've tried using useState but it didn't resolve the issue. How can I debug and fix this?",
  "missing_information": [
    {
      "category": "context",
      "question": "What exactly happens when the button 'doesn't work'? (No response, error, wrong action?)",
      "importance": "critical"
    },
    {
      "category": "context",
      "question": "Can you share the button component code?",
      "importance": "critical"
    },
    {
      "category": "context",
      "question": "Is there a pattern to when it fails? (specific users, timing, browser, network conditions?)",
      "importance": "high"
    },
    {
      "category": "scope",
      "question": "Are the framework switch and API performance separate issues you want addressed, or focus only on the button?",
      "importance": "high"
    },
    {
      "category": "constraint",
      "question": "What debugging have you already tried besides useState?",
      "importance": "medium"
    }
  ],
  "detected_issues": [
    {
      "issue": "buried_intent",
      "description": "Core issue (button bug) is hidden among project history and unrelated concerns",
      "resolution_strategy": "Extract and focus on button debugging first; defer framework/API discussion"
    },
    {
      "issue": "missing_context",
      "description": "No details on failure mode, button code, or reproduction steps",
      "resolution_strategy": "Ask for code snippet and failure description before attempting solution"
    },
    {
      "issue": "xy_problem",
      "description": "User jumped to 'framework switch' as solution without diagnosing button issue",
      "resolution_strategy": "Address immediate button bug first; framework choice is separate discussion"
    },
    {
      "issue": "multiple_intents",
      "description": "Three separate topics: button bug, framework choice, API performance",
      "resolution_strategy": "Prioritize: 1) Button bug (critical), 2) Framework discussion (defer), 3) API (separate issue)"
    }
  ],
  "recommended_next_steps": [
    "Ask user to share button component code",
    "Ask for specific error messages or console output",
    "Ask for pattern: when does button fail vs. succeed?",
    "Once button is fixed, optionally discuss framework/API concerns"
  ]
}
```

---

## Example 2: Simple Case

**Input**:
```
help with code
```

**Output**:
```json
{
  "original_input": "help with code",
  "confusion_analysis": {
    "primary_pattern": "missing_info",
    "word_count": 3,
    "tangents_detected": [],
    "redundancy_level": "low"
  },
  "extracted_intent": {
    "core_need": "Needs assistance with code (unspecified)",
    "secondary_needs": [],
    "confidence": "low"
  },
  "clarified_request": "User needs help with code but hasn't specified the problem, language, or goal.",
  "missing_information": [
    {
      "category": "context",
      "question": "What code needs help? (Please share the code or describe the file)",
      "importance": "critical"
    },
    {
      "category": "context",
      "question": "What programming language?",
      "importance": "critical"
    },
    {
      "category": "context",
      "question": "What's the problem? (Error, unexpected behavior, need explanation, want to improve?)",
      "importance": "critical"
    },
    {
      "category": "context",
      "question": "What have you tried so far?",
      "importance": "medium"
    }
  ],
  "detected_issues": [
    {
      "issue": "missing_context",
      "description": "Extremely vague request with no actionable information",
      "resolution_strategy": "Ask clarifying questions before attempting to help"
    }
  ],
  "recommended_next_steps": [
    "Ask user to provide: 1) The code, 2) Programming language, 3) Description of the problem",
    "Once details provided, re-assess and proceed with specific help"
  ]
}
```

---

## Usage Notes

- **When to use**: User input is unclear, verbose, or contains multiple topics
- **When NOT to use**: Request is already clear and concise
- **Best practice**: Show clarified request to user for confirmation before proceeding
- **Iteration**: If user says clarification is wrong, ask more specific questions

---

## Quality Checklist

- [ ] Core intent extracted and stated concisely
- [ ] Tangents and redundancy identified
- [ ] Missing information clearly listed with importance
- [ ] Detected issues explained with resolution strategies
- [ ] Disambiguation questions are specific and actionable
- [ ] Recommended next steps are concrete
- [ ] Confidence level assessed honestly
- [ ] Multiple intents separated if present
