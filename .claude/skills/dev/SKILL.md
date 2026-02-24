---
name: dev
description: TDD-style development — clarify requirements, write failing test, implement, verify.
---

# Dev Skill

Trigger: `/dev <description of what to build or change>`

Develop a feature or change using test-driven development: clarify → failing test → implement → verify.

## Process

### 1. Clarify the requirement

**Do NOT skip this step. Do NOT write tests first.**

- **Do you understand the requirement well enough to write a test for it?** If the description is ambiguous — you're unsure about inputs, outputs, or edge cases — **ask clarifying questions before doing anything else.**
- **Only ask about the requirement itself** — don't ask questions you can answer by reading the codebase (e.g. where code should live, existing conventions, file structure). Read the code to figure those out yourself.
- Keep questions focused and minimal. Ask only what's needed to write the first test.

### 2. Establish a baseline

- **Run the existing tests** to see which tests currently pass and fail.
- This baseline lets you detect if your changes cause regressions later in step 4.

### 3. Write a failing test

- **Find the right test file**: Check for existing tests related to the affected module. Add to an existing file when it fits; create a new one only if no suitable file exists.
- **Write the simplest test** that captures the core requirement. Don't test edge cases yet — get the main behavior first.
- **Run the test** and confirm it fails for the right reason (missing function, wrong return value — not a syntax error or import problem).
- If the test passes already, the feature may already exist — investigate before proceeding.

### 4. Implement

- **Make the minimal change** that makes the test pass. Don't add extras, don't refactor, don't handle edge cases yet.

### 5. Verify

- **Run type checking** to catch type errors.
- **Run linting** and fix any warnings or errors.
- **Run the test** from step 3 and confirm it passes.
- **Run all tests** and compare against the baseline from step 2 to check for regressions.
- **If tests fail**: Fix the implementation (not the test, unless the test is wrong). Go back to step 4.
- **If tests pass**: Proceed to step 6.

### 6. Refactor

Now that the test is green, look at the code you touched (implementation **and** tests) and apply:
- **DRY**: Is there duplication that can be extracted?
- **SRP**: Is any function/module doing more than one thing?
- **Dead code**: Did your changes make any existing code unreachable or unused? Remove it.
- **Run linting, then tests after every refactoring change** — the green tests protect you.
- Keep refactoring small and incremental. Don't redesign — just clean up.

### 7. Next requirement or wrap up

- **Are there more requirements?** If the original request has multiple aspects, go back to step 3 with the next requirement. Add one test at a time, implement, verify, refactor.
- **Edge cases**: Once the core behavior works, add tests for edge cases if they're part of the requirement.
- **When done**: Run type checking and linting one final time. Summarize what was built.
- **Do NOT commit** — leave that to the user.

## TDD loop

Steps 3–6 form the core loop. Each iteration adds one behavior:

```
Failing test → Implement → Verify → Refactor
      ↑                                 |
      |         (next requirement)       |
      +---------------------------------+
```

## Guidelines

- **Test first, always**: Never write implementation before the test. The failing test is your specification.
- **Minimal changes**: Only write what's needed to pass the current test. YAGNI.
- **One behavior at a time**: Each loop iteration should add exactly one testable behavior.
- **Don't read broadly**: Only read code that's directly relevant to the current test/implementation step. Avoid exploring the codebase for context you don't need yet.
- **Existing patterns**: When you do read related code, follow its conventions (naming, file structure, test style).
- **Test isolation**: Unit tests must not depend on external state or services. Mock external dependencies.
