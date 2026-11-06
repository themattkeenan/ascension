---
name: completion-gate
description: Use when about to declare work done, fixed, or passing, before committing, opening a PR, or moving to the next task, and when a delegated agent reports it has finished
---

# Completion Gate

A claim of success stands next to the verification output that supports it, produced in the same message as the claim. The failure this prevents: declaring work done on the strength of a prior run, a hunch, or an agent's self-report.

## When to Use

- Before any statement that the work is done, fixed, passing, or correct, in any wording: paraphrases and implications count the same as the exact words
- Before committing, opening a PR, or marking a task complete
- Before moving on to the next task
- When a delegated agent reports it has finished

## The Entry Protocol

Before asserting any status or expressing confidence:

1. **Identify** which command substantiates the assertion.
2. **Execute** the full command, fresh, in this message.
3. **Inspect** every line of output: exit code, failure tally.
4. **Confirm** the output supports the assertion. If it does not, report the actual status with the evidence. If it does, state the assertion with the evidence beside it.

Done when the assertion in your message sits next to output from a run in that same message, and the output says what the assertion says.

## Verification Requirements

| Assertion | Demands | Insufficient |
|-----------|---------|--------------|
| Tests pass | Test runner output showing 0 failures | Prior run, "should pass" |
| Linter clean | Linter output showing 0 errors | Partial scan, extrapolation |
| Build succeeds | Build command with exit code 0 | Linter passing, log fragments |
| Bug resolved | Original symptom tested and passes | Code modified, assumed fixed |
| Regression test valid | Red-green cycle confirmed | Single green pass |
| Agent finished | VCS diff showing actual changes | Agent self-reported "done" |
| Specification met | Line-by-line requirement checklist | Tests passing alone |

## Verification Patterns

**Tests:**
```
CORRECT: [Run test command] [Output: 34/34 pass] "All tests pass"
WRONG: "Should pass now" / "Looks correct"
```

**Regression tests (Red-Green cycle):**
```
CORRECT: Write -> Run (pass) -> Revert fix -> Run (MUST FAIL) -> Restore -> Run (pass)
WRONG: "I've written a regression test" (without red-green confirmation)
```

**Build:**
```
CORRECT: [Run build] [Output: exit 0] "Build passes"
WRONG: "Linter passed" (linter does not validate compilation)
```

**Specification compliance:**
```
CORRECT: Re-read plan -> Create checklist -> Verify each item -> Report gaps or completion
WRONG: "Tests pass, phase done"
```

**Agent delegation:**
```
CORRECT: Agent reports done -> Check VCS diff -> Verify changes -> Report actual state
WRONG: Trust agent report at face value
```

## Verifying Configuration Changes

When testing changes to configuration, providers, feature flags, or environment settings:

**Do not merely confirm the operation succeeded. Confirm the output reflects the intended change.**

### The Silent Fallback Problem

An operation can succeed because *some* valid configuration exists, even if it is not the configuration you intended to apply.

| Change | Insufficient | Required |
|--------|-------------|----------|
| Switch LLM provider | HTTP 200 | Response body contains expected model identifier |
| Toggle feature flag | No errors | Feature behavior observably active |
| Change environment | Deployment succeeds | Logs/variables reference the new environment |
| Set credentials | Authentication succeeds | Authenticated identity is the correct one |

### Configuration Verification Sequence

1. **Identify** what should be different after this change.
2. **Locate** where that difference is observable.
3. **Execute** the command that exposes the observable difference.
4. **Confirm** the output contains the expected difference.

Done when the output shows the difference itself, not just the absence of errors: a status code without the response body, or "request succeeded" without inspecting the content, is not done.

## Verifying UI Work

When asserting UI work is complete:

| Assertion | Demands | Insufficient |
|-----------|---------|--------------|
| Component matches design | Visual comparison against UX reference | "It looks right to me" |
| Responsive design works | Tested at mobile, tablet, desktop breakpoints | Desktop-only check |
| Accessibility passes | Screen reader test + keyboard navigation + contrast check | "I added aria labels" |
| Design tokens applied | Spacing/colors/typography match token values | "I used the right classes" |

## Guardrails

- Run the command before any phrasing that implies success; the output decides, not confidence.
- Where a hedge appears in your draft ("should", "probably", "seems to"), replace it with a run and report what the run showed.
- Treat a linter pass as a lint result only; the build and the tests each need their own run.
- Treat an agent's "done" as a claim to check: read the VCS diff and run the verification yourself.
- A partial or stale check covers only what it ran; run the full command before claiming the whole.

Stop and run the verification when you notice: hedging language in your draft, "Done!" or "Perfect!" before any command ran in this message, a commit, push, or PR about to happen without a run, or the urge to wrap up.

## Related

`ascension:quality-enforcement` supplies the checks whose output is the evidence here. `ascension:test-first` owns the red-green cycle this gate confirms. `ascension:merge-protocol` runs after this gate passes.
