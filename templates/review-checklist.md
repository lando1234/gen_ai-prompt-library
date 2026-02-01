# Prompt Review Checklist

Use this checklist when reviewing prompts for inclusion in the library.

---

## 1. Structure and Format

- [ ] Follows [prompt-template.md](prompt-template.md) exactly
- [ ] All required sections present: Usage, Inputs, Outputs, Prompt
- [ ] Metadata block is complete and accurate
- [ ] File naming follows `<verb>-<object>-<qualifier>.md` convention
- [ ] Title is clear and follows pattern: `# [Verb] [Object] [Context]`
- [ ] Prompt section is a single, uninterrupted, copy-pasteable block

---

## 2. Metadata Quality

- [ ] `type` is correctly assigned to one primary category
- [ ] `complexity` matches the actual difficulty level
- [ ] `context` accurately reflects technical/business/hybrid nature
- [ ] `output_format` matches what the prompt actually produces
- [ ] `interaction_mode` is realistic (single-shot vs. iterative vs. conversational)
- [ ] `production_ready` status is honest (yes = battle-tested, experimental = untested)
- [ ] `tags` are relevant and useful for discovery

---

## 3. Clarity (Score: /5)

- [ ] Instructions are unambiguous
- [ ] Technical terms are defined or contextually clear
- [ ] No vague directives ("make it better", "use best practices")
- [ ] Structure is logical and easy to follow
- [ ] Constraints are front-loaded and explicit

**Score Rationale:**
```
[Explain why you gave this score]
```

---

## 4. Completeness (Score: /5)

- [ ] All required inputs are explicitly listed
- [ ] Input types and formats are specified
- [ ] Output format is fully defined (structure, schema, examples)
- [ ] Success criteria are stated
- [ ] Edge cases are addressed or noted
- [ ] Failure modes are considered

**Missing Elements (if any):**
```
[List what needs to be added]
```

**Score Rationale:**
```
[Explain why you gave this score]
```

---

## 5. Reusability (Score: /5)

- [ ] No hardcoded company-specific values
- [ ] No overfitting to a single scenario
- [ ] Can be adapted across different contexts
- [ ] Uses parameterization, not hardcoding
- [ ] Assumptions are portable or explicitly stated

**Reusability Concerns (if any):**
```
[Note any specificity that limits reuse]
```

**Score Rationale:**
```
[Explain why you gave this score]
```

---

## 6. Production Readiness (Score: /5)

- [ ] Has been tested in real usage (if marked production_ready: yes)
- [ ] Handles common failure scenarios
- [ ] Validates inputs appropriately
- [ ] Degrades gracefully when constraints are violated
- [ ] Provides clear success/failure signals

**Production Risks (if any):**
```
[Note any production concerns]
```

**Score Rationale:**
```
[Explain why you gave this score]
```

---

## 7. Examples and Documentation

- [ ] Usage section clearly explains when/why to use this prompt
- [ ] Inputs section includes example values
- [ ] Outputs section includes realistic example output
- [ ] Notes section documents edge cases or limitations (if applicable)
- [ ] Related prompts are cross-referenced (if applicable)

---

## 8. Taxonomy Placement

- [ ] Correctly placed in the right category
- [ ] Category is the best fit (not forced)
- [ ] If specialized, subcategory is appropriate
- [ ] No duplication with existing prompts
- [ ] If similar to existing prompts, justification for new prompt is clear

**Category Justification:**
```
[Explain why this category is correct]
```

---

## Overall Assessment

**Total Score:** ____ / 25 (sum of 5 scored dimensions)

**Quality Gate Decision:**
- [ ] **Accept**: All dimensions ≥ 3, at least two = 5, total ≥ 18
- [ ] **Conditional Accept**: All dimensions ≥ 3, total ≥ 15, specific improvements noted
- [ ] **Revise and Resubmit**: Any dimension < 3 or total < 15
- [ ] **Reject**: Multiple dimensions ≤ 2, fundamental issues, or duplicate

---

## Required Improvements (if Conditional Accept or Revise)

### Critical Issues (Must Fix):
```
1. [Issue and how to fix]
2. [Issue and how to fix]
```

### Recommended Improvements (Should Fix):
```
1. [Suggestion]
2. [Suggestion]
```

### Optional Enhancements (Nice to Have):
```
1. [Enhancement idea]
2. [Enhancement idea]
```

---

## Reviewer Notes

**What Works Well:**
```
[Highlight strengths]
```

**Key Concerns:**
```
[Summarize main issues]
```

**Overall Recommendation:**
```
[Final verdict and reasoning]
```

---

**Reviewer:** [Name]
**Date:** [YYYY-MM-DD]
**Review Duration:** [Time spent reviewing]
