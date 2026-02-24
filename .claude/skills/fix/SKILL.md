---
name: fix
description: Systematically debug a bug through investigation, fixing, and verification.
---

# Fix Skill

Trigger: `/fix <description of the bug or where it was observed>`

Systematically find and fix a bug by iterating through investigation, fixing, and verification until the bug is resolved.

## Process

### 1. Reproduce the bug

**Do NOT skip this step. Do NOT read code first.**

- **First: Do you understand the bug well enough to reproduce it?** If the description is ambiguous — you're unsure about the trigger conditions, frequency, or exact symptoms — **ask clarifying questions before doing anything else.** Don't guess and don't start reading code to fill in the gaps.
- **Then: Find or write a test** that demonstrates the bug:
  - **Unit/integration test**: If the bug is in a library function, API route, or logic module, write a failing test.
  - **E2E test**: If the bug is in the UI, involves timing/race conditions, or requires a running app, use an E2E test to reproduce it in the browser.
  - Check for existing tests related to the affected area first.
- **Run the test** and confirm it fails. You must see the failure before moving on. If the test passes, your reproduction is wrong — fix the test, not the code.

### 2. Investigate (locate the root cause)

Narrow down the root cause by **running code**, not just reading it. Each investigation step should produce observable output.

1. **Start at the symptom**: Find where the bad output is produced (e.g. the code that renders the error state), then **trace backward** through the call chain.
2. **Add temporary logging** at decision points along this path and **run the reproduction** to see actual values:
   - Log at conditionals, before/after transformations, and where data flows between modules.
   - Run the test or E2E scenario and read the output.
   - Use the output to narrow down which section behaves unexpectedly.
3. **Narrow the scope iteratively**: Based on log output, add more logging closer to where the value diverges from expected, then run again.
4. **Read source code only as needed** to understand the code around the narrowed-down location — don't read broadly, read where the logs point you.
5. **Consider environment factors**: Could the bug be caused by HMR (module state reset), race conditions, caching, or dev-vs-prod differences?
6. **Check recent changes** if the above doesn't reveal the cause: `git log --oneline -20` and `git diff` to see if a recent change introduced the bug.

**Important**: Every investigation round must include running something and observing output. Don't just read code and guess — add logging, run, observe.

### 3. Fix the bug

- **Make the minimal fix** that addresses the root cause. Don't refactor surrounding code or fix unrelated issues.
- **Keep temporary logging in place** — it's still needed to verify the fix.

### 4. Verify the fix

- **Run type checking** to catch type errors introduced by the fix.
- **Run linting** to catch style and correctness issues introduced by the fix.
- **Run the regression test** (from step 1) and confirm it passes.
- **Run related tests** for the affected module.
- **If tests pass**: The bug is fixed. Proceed to step 5.
- **If tests fail or the bug persists**: Go back to step 2 (Investigate) with the new information learned. The fix was either wrong or incomplete — don't keep guessing, re-investigate.

### 5. Refactor

Now that the fix is verified, look at the code you touched (fix **and** tests) and apply:
- **DRY**: Is there duplication that can be extracted?
- **SRP**: Is any function/module doing more than one thing?
- **Dead code**: Did your changes make any existing code unreachable or unused? Remove it.
- **Run linting, then tests after every refactoring change** — the green tests protect you.
- Keep refactoring small and incremental. Don't redesign — just clean up.

### 6. Clean up and wrap up

- **Remove all temporary logging** added during investigation (console.log, debug flags).
- Run type checking and linting one final time.
- Summarize what the root cause was and what the fix does.
- **Do NOT commit** — leave that to the user.

## Investigation loop

Steps 2–4 form a loop (with refactoring in step 5 once verified). Each iteration should make progress:

```
Investigate → Fix → Verify
     ↑                  |
     |    (if still broken)
     +------------------+
```

If after 3 iterations the root cause is still unclear, **stop and present findings to the user**:
- What was checked and ruled out
- What the most likely remaining hypotheses are
- What additional context or access would help

## Guidelines

- **Minimal changes**: Only modify what's necessary to fix the bug. Don't add error handling, refactoring, or improvements beyond the fix.
- **One bug at a time**: If you discover additional bugs during investigation, note them but don't fix them unless they're the root cause.
- **Preserve behavior**: The fix should only change the broken behavior, not alter working functionality.
- **Automated tests**: Always try to leave behind a regression test so the bug can't recur silently. If a regression test isn't practical (e.g. the bug requires simulating environment conditions that can't be reproduced in a test runner), document why in the summary.
