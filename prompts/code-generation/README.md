# Code Generation

Prompts that **produce executable code** with strong output contracts.

---

## What Belongs Here

- Function and class generation
- API endpoint implementation
- Test generation (unit, integration, e2e)
- Refactoring and code transformation
- Boilerplate and scaffolding generation
- Script and automation code

---

## Characteristics

- **Output is runnable code**: The result can be executed
- **Explicit language and framework**: No ambiguity about the tech stack
- **Clear success criteria**: Tests pass, meets spec, follows style guide
- **Defined scope**: Single function, single file, or clearly bounded module

---

## Complexity Range

**Basic → Advanced**

- **Basic**: Single function, clear spec, common language
- **Intermediate**: Multiple functions, framework knowledge required
- **Advanced**: Complex algorithms, performance requirements, architectural constraints

---

## Examples of Prompts That Belong Here

- `generate-rest-api-endpoint.md`
- `create-unit-tests-for-function.md`
- `refactor-code-for-readability.md`
- `generate-data-validation-logic.md`
- `create-orm-model-from-schema.md`

---

## Examples of Prompts That DON'T Belong Here

- Architecture design (→ `system-design/`)
- Code review or audit (→ `analysis/`)
- Technical documentation (→ `specialized/` or `strategic/`)
- Prompt generation (→ `meta-prompts/`)

**Rule of thumb:** If the output is code that can be run or tested, it belongs here.

---

## Best Practices for Code Generation Prompts

1. **Specify language and version**: "Python 3.11+", not just "Python"
2. **Define style requirements**: PEP 8, Google style, etc.
3. **Include test requirements**: "Include docstrings and type hints"
4. **State framework/library versions**: "FastAPI 0.104+", "React 18+"
5. **Define success criteria**: "Passes mypy", "lint-free", "test coverage > 80%"
