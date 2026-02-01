# Prompt Engineering Principles

This document articulates the **design philosophy** behind prompts in this library. These are not rules carved in stone — they're heuristics derived from production experience.

---

## Core Tenets

### 1. Prompts Are Contracts, Not Suggestions

A well-designed prompt is a **contract between user and model**:
- **Inputs**: What you provide
- **Outputs**: What you get back
- **Guarantees**: What's promised
- **Failure modes**: What can go wrong

Treat prompt design like API design. Be explicit, predictable, and robust.

---

### 2. Clarity Beats Cleverness

A prompt that "sounds smart" but confuses the model (or user) is a failed prompt.

**Bad:**
```
Synthesize a comprehensive, multi-faceted analysis leveraging
industry best practices and paradigmatic frameworks.
```

**Good:**
```
Analyze the system architecture and identify:
1. Single points of failure
2. Scalability bottlenecks
3. Security vulnerabilities

For each issue, provide:
- Severity (low/medium/high)
- Specific location in the architecture
- Recommended fix
```

**Why?**
- The "good" version has clear structure
- The model knows exactly what to produce
- The user can verify completeness

---

### 3. Context Is King, But Too Much Is Chaos

**Goldilocks principle for context:**
- Too little → Model guesses, gets it wrong
- Too much → Model gets lost, produces noise
- Just right → Enough to constrain, not overwhelm

**Guidelines:**
- Include domain-specific context if it changes behavior
- Omit context the model already knows (e.g., "Python is a programming language")
- Front-load critical constraints ("You MUST return valid JSON")

**Example:**
```
# Too little
Write a function to process data.

# Too much
Python is a programming language created by Guido van Rossum in 1991.
It is widely used for data science and web development. Functions in Python
use the 'def' keyword and can accept parameters. Please write a function
that processes data, keeping in mind Python's design philosophy...

# Just right
Write a Python function that:
- Accepts a list of dictionaries
- Filters out items where 'status' != 'active'
- Returns a list of 'id' values
- Includes type hints and a docstring
```

---

### 4. Constrain the Output, Not Just the Input

Models are creative. Without output constraints, you get variety — sometimes useful, often chaotic.

**Output constraints to consider:**
- **Format**: JSON, Markdown, code, CSV
- **Schema**: Specific structure, required fields
- **Length**: Token count, character limit, number of examples
- **Style**: Formal, concise, step-by-step
- **Content boundaries**: "Do not include X", "Focus only on Y"

**Example:**
```
Generate a summary of this article.

Output requirements:
- Exactly 3 bullet points
- Each bullet: max 20 words
- Focus on actionable insights, not background
- Use present tense
```

---

### 5. One Job, One Prompt

Prompts that try to do too much produce mediocre results at everything.

**Anti-pattern:**
```
Analyze this codebase for bugs, suggest refactorings, write unit tests,
document the API, and propose a microservices architecture.
```

**Better:**
Break into discrete prompts:
1. `analyze-code-for-bugs.md`
2. `suggest-refactoring-opportunities.md`
3. `generate-unit-tests.md`
4. `document-api-endpoints.md`
5. `design-microservices-architecture.md`

Then, if needed, create a **workflow prompt** that orchestrates these.

**Why?**
- Each prompt can be refined independently
- Easier to test and validate
- Reusable in different contexts

---

### 6. Examples Are Force Multipliers

One good example is worth 100 words of explanation.

**When to use examples:**
- Complex output formats
- Ambiguous instructions
- Novel tasks the model hasn't seen often

**How to use examples:**
- Show input → output pairs
- Annotate what makes the example good
- Provide 2-3 examples, not just one (to show variation)

**Template:**
```
Generate a commit message following this format:

Example 1:
Input: Added user authentication
Output: feat(auth): implement JWT-based user authentication

Example 2:
Input: Fixed login bug
Output: fix(auth): resolve token expiration edge case

Now generate a commit message for:
Input: {user_change_description}
```

---

### 7. Chain Prompts for Complex Tasks

Some tasks are inherently multi-step. Don't force them into one prompt.

**Chaining strategies:**
1. **Sequential**: Output of Prompt A → Input to Prompt B
2. **Conditional**: Based on Prompt A output, choose Prompt B or C
3. **Iterative**: Prompt A → Refine → Prompt A again
4. **Parallel**: Multiple prompts run independently, then synthesized

**Example workflow:**
```
Step 1: analyze-requirements.md
  → Outputs: List of features, constraints, risks

Step 2: design-architecture.md
  → Inputs: Features from Step 1
  → Outputs: System design document

Step 3: generate-implementation-plan.md
  → Inputs: Architecture from Step 2
  → Outputs: Task breakdown with priorities
```

Document these as **workflow prompts** in the `prompts/workflows/` category.

---

### 8. Fail Gracefully, Not Silently

Models will produce bad outputs. Your prompt should make failures **obvious and debuggable**.

**Strategies:**
- Request explicit uncertainty markers ("If unsure, state 'UNKNOWN'")
- Ask for confidence scores ("Rate confidence: low/medium/high")
- Include validation steps ("Check: Does output satisfy constraint X?")
- Define failure outputs ("If task is impossible, return: { 'error': '...' }")

**Example:**
```
Generate SQL query for: {natural_language_request}

If the request is ambiguous, return:
{
  "status": "clarification_needed",
  "question": "Which table should I query: users or accounts?"
}

If the request is impossible, return:
{
  "status": "error",
  "reason": "Cannot join tables X and Y without a common key"
}

Otherwise, return:
{
  "status": "success",
  "query": "SELECT ..."
}
```

---

### 9. Optimize for Human Readability First

Prompts are read more often than they're written (just like code).

**Readability tactics:**
- Use headings, lists, and structure
- Break long instructions into numbered steps
- Use whitespace generously
- Define acronyms and jargon
- Front-load the most important constraints

**Avoid:**
- Wall-of-text paragraphs
- Nested subclauses ("The function, which should be written in Python, unless JavaScript is preferred, should...")
- Ambiguous pronouns ("Use it to process them" — what is "it"? what are "them"?)

---

### 10. Version and Iterate

No prompt is perfect on the first try. Treat them like code:

- **Track changes**: Use git to manage prompt evolution
- **Test with real inputs**: Don't just theorize, run it
- **Collect failure cases**: When it breaks, document why
- **Refine iteratively**: Each failure mode is a refinement opportunity

**Versioning strategy:**
- File history in git serves as version control
- Add a "Notes" section to document known issues and improvements
- Mark maturity: `production_ready: yes` vs. `experimental`

---

## Anti-Patterns to Avoid

### 1. The Overloaded Prompt
Trying to do 5 things in one prompt. Split into discrete prompts or workflows.

### 2. The Vague Directive
"Make it better." Better how? By what criteria?

### 3. The Implicit Assumption
"Use the standard format." Whose standard? Define it explicitly.

### 4. The Wishful Thinking
"Generate perfect, bug-free code." Models can't guarantee perfection. Set realistic expectations.

### 5. The Jargon Bomb
Unnecessary complexity. Use domain terms only when they add precision.

### 6. The One-Shot Wonder
Designed for a single use case, not reusable. Generalize or mark as specialized.

### 7. The Silent Failure
Model produces garbage, but prompt provides no validation or error handling.

---

## Testing Your Prompt

Before cataloging, ask:

1. **Clarity test**: Can someone unfamiliar with the domain use this?
2. **Edge case test**: What happens with empty input? Invalid input? Ambiguous input?
3. **Consistency test**: Does it produce similar quality across different valid inputs?
4. **Failure mode test**: When it fails, is it obvious and debuggable?
5. **Reusability test**: Can this be adapted to other contexts?

If any test fails, refine before submitting.

---

## Learning from Failures

The best prompts emerge from failures. When a prompt breaks:

1. **Document the failure**: What input caused it? What was the bad output?
2. **Identify the root cause**: Was the prompt ambiguous? Missing constraints? Wrong assumptions?
3. **Refine and test**: Add constraints, examples, or validation
4. **Update the prompt**: Make the improvement, document in "Notes"

This library should be a **living record of lessons learned**, not a static collection.

---

## Recommended Reading

For deeper understanding of prompt engineering:

- OpenAI's prompt engineering guide
- Anthropic's guide to prompting Claude
- "The Prompt Report" (academic survey of prompting techniques)
- Real-world case studies from production GenAI systems

But remember: **principles matter more than tricks**. A well-structured, clear, explicit prompt will outperform a "clever hack" every time.

---

**Last Updated**: 2026-02-01
