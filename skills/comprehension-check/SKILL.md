---
name: comprehension-check
description: Use after a substantial feature, multi-file change, or architectural change and before committing it, or when the user asks what changed or how it works - produces a plain-language walkthrough of every alteration so the developer can confirm they understand what is shipping
---

# Comprehension Check

The developer can explain every change in ordinary language before it is committed. The failure this prevents: shipping AI-generated code faster than anyone absorbs it, leaving cognitive debt that nobody can troubleshoot, extend, or reason about later.

## When to Use

**Triggered after:**
- Any modification touching 3 or more files
- Non-trivial algorithm or data-structure implementations
- Structural shifts (new modules, altered data flow, dependency rewiring)
- Security-adjacent code (authentication, encryption, input sanitization)
- Autonomous agent decisions (changes you did not explicitly dictate)

**Skippable for:**
- Single-character or typo corrections
- Changes the developer dictated verbatim, line by line
- Pure boilerplate generation (scaffolding configs, lockfiles)

## The Process

### Phase 1: Produce a Change Walkthrough

After implementation completes, before any commit occurs, for every modified file articulate:

1. **What:** what is different now? (Describe the meaning, not the diff)
2. **Why:** what motivated this particular change?
3. **Context:** how does this change interact with the broader modification set?
4. **Hazard:** what failure modes does this change introduce?

Done when every file in the diff has all four.

### Phase 2: Deliver the Walkthrough

Structure the output as follows:

```markdown
## Modification Walkthrough

### [path/to/first-file]
**What:** Introduced sliding-window rate limiting that counts requests per IP address.
**Why:** The login endpoint was exposed to brute-force enumeration.
**Context:** Middleware executes ahead of authentication routes; relies on Redis for distributed state.
**Hazard:** If Redis becomes unreachable, the limiter fails open (permits all traffic). Worth discussing: should it fail closed instead?

### [path/to/second-file]
**What:** Login handler now returns HTTP 429 when the rate limiter activates.
**Why:** The middleware flags rate-limited requests via `req.rateLimited`.
**Context:** Early exit occurs before password verification, which also blocks timing-based attacks.
**Hazard:** Negligible -- a simple conditional guard.

## Structural Consequences
- New runtime dependency: Redis (stores rate-limit counters)
- New middleware layer inserted between routing and handler execution
- Login request lifecycle now: rate-check -> authenticate -> respond

## Open Questions
1. Should the rate limiter deny all traffic when Redis is unavailable, or allow it?
2. Should rate limiting extend beyond authentication endpoints?
```

### Phase 3: Obtain Explicit Confirmation

**Ask directly:** "Do you fully understand these changes and want to proceed with the commit?"

When the developer raises questions, resolve each one fully. Done when the developer has said yes and no question of theirs is left standing; confusion left unresolved is cognitive debt accruing.

### Phase 4: Surface Unsolicited Changes

Whenever a change was not part of the original request:

```
HEADS UP: I additionally [modified X] because [rationale].
This was NOT part of your original instruction. Keep it or revert?
```

Every change outside the original request is disclosed this way.

## Indicators of Genuine Comprehension

The developer should be able to:
- [ ] Describe what each altered file does differently now
- [ ] Justify why each alteration was necessary
- [ ] Predict what would break if a specific change were rolled back
- [ ] Enumerate any new dependencies or patterns that were introduced
- [ ] Identify where to investigate if a bug surfaces in this code later

## Guardrails

- Every multi-file modification gets a walkthrough, and the developer reads it before the commit.
- Every change beyond the original request is disclosed with a keep-or-revert question.
- The commit waits on explicit confirmation.
- Resolve every developer question fully before proceeding.
- Treat green tests as evidence of behaviour, not of comprehension; the walkthrough still runs.
- "Mostly boilerplate" still gets the walkthrough; only pure scaffolding and lockfiles are skipped.

## Related

Runs on the output of `ascension:delegated-execution`, after code is written and tests pass, and before `ascension:completion-gate` verifies and `ascension:merge-protocol` lands the work.
