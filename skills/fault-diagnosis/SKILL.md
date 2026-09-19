---
name: fault-diagnosis
description: Use when encountering any bug, test failure, performance regression, or unexpected behavior, before proposing fixes
---

# Fault Diagnosis

A discipline for hard bugs. Six phases. Skip one only with an explicit reason.

While exploring, read `CONTEXT.md` if it exists so you name things the way the project does, and check the ADRs covering the area you are touching.

## Redact first

This skill has you show commands, outputs, and captured artifacts. **Redact every secret before showing it**: write `<REDACTED>` in its place. Build loops against environment variables so the credential stays in the environment rather than in what you print. Captured artifacts carry auth headers — quote only the lines that carry the signal.

If the redacted output is not enough to diagnose the bug, say so and ask.

## Phase 1: Build a tight feedback loop

**This is the skill.** Everything after it is mechanical. With a **tight** pass/fail signal — one that goes red on *this* bug — you will find the cause; bisection, hypothesis-testing, and instrumentation all just consume it. Without one, no amount of staring at code will save you.

Spend disproportionate effort here. Be aggressive, be creative, refuse to give up.

### Ways to build one, in roughly this order

1. **Failing test** at whatever seam reaches the bug — unit, integration, e2e.
2. **Curl or HTTP script** against a running dev server.
3. **CLI invocation** with a fixture input, diffing stdout against a known-good snapshot.
4. **Headless browser script** (Playwright, Puppeteer) driving the UI and asserting on DOM, console, or network.
5. **Replay a captured trace** — save a real request, payload, or event log to disk and replay it through the code path in isolation.
6. **Throwaway harness** — a minimal subset of the system (one service, mocked deps) that hits the bug path in a single function call.
7. **Property or fuzz loop** — for "sometimes wrong output", run a thousand random inputs and look for the failure mode.
8. **Bisection harness** — if it appeared between two known states (commit, dataset, version), automate "boot at state X, check, repeat" so `git bisect run` can drive it.
9. **Differential loop** — same input through old versus new, or two configs, diffing outputs.
10. **Human-in-the-loop script** — last resort. If a human must click, drive *them* with a script that prompts each step and captures the result, so the loop is still structured.

### Tighten it

Treat the loop as a product. Once you have *a* loop:

- **Faster** — cache setup, skip unrelated init, narrow the scope.
- **Sharper** — assert the specific symptom, not "didn't crash".
- **More deterministic** — pin time, seed RNG, isolate the filesystem, freeze the network.

A 30-second flaky loop is barely better than none. A 2-second deterministic one is a superpower.

**Non-deterministic bugs:** the goal is not a clean repro but a **higher reproduction rate**. Loop the trigger 100x, parallelise, add stress, narrow timing windows, inject sleeps. A 50%-flake bug is debuggable; a 1% one is not, so keep raising the rate until it is. For test pollution specifically, `find-polluter.sh` in this directory bisects the suite for you.

### Completion criterion

Phase 1 is done when you can name **one command** — a script path, a test invocation, a curl — that you have **already run at least once** (show the invocation and its redacted output), and that is:

- [ ] **Red-capable** — it drives the actual bug path and asserts the **user's exact symptom**, so it goes red on this bug and green once fixed. Not "runs without erroring".
- [ ] **Deterministic** — same verdict every run (or a pinned, high reproduction rate).
- [ ] **Fast** — seconds, not minutes.
- [ ] **Runnable unattended.**

Catch yourself reading code to build a theory before that command exists, and stop: jumping to a hypothesis is the exact failure this skill prevents. No red-capable command, no Phase 2.

### When you genuinely cannot build one

Say so explicitly and list what you tried. Ask for one of: access to an environment that reproduces it, a redacted captured artifact (HAR file, log dump, core dump, screen recording with timestamps), or permission to add temporary production instrumentation. Do not proceed to hypothesise without a loop.

## Phase 2: Reproduce and minimise

Run the loop. Watch it go red.

Confirm:

- [ ] It produces the failure **the user described**, not a different failure nearby. Wrong bug, wrong fix.
- [ ] It reproduces across runs (or at a high enough rate to work against).
- [ ] You captured the exact symptom — error text, wrong output, the timing — so later phases can verify the fix addresses *it*.

Then **minimise**: shrink to the smallest scenario that still goes red. Cut inputs, callers, config, data, and steps **one at a time**, re-running after each cut. Done when every remaining element is load-bearing — removing any one makes it go green.

A minimal repro shrinks the hypothesis space in Phase 3 and becomes the clean regression test in Phase 5.

## Phase 3: Hypothesise

Generate **3-5 ranked hypotheses before testing any of them.** Generating one anchors you on the first plausible idea.

Each must be **falsifiable** — state the prediction it makes:

> "If X is the cause, then changing Y makes the bug disappear / changing Z makes it worse."

Cannot state the prediction? It is a vibe. Discard or sharpen it.

**Show the ranked list to the user before testing.** They often re-rank it instantly ("we just deployed a change to #3") or know which ones they have already ruled out. Cheap checkpoint, big saving. Do not block on it — proceed with your ranking if they are away.

When the error is deep in the call stack, `root-cause-tracing.md` in this directory has the backward-tracing method: find where the bad value originates, not where it surfaces.

## Phase 4: Instrument

Each probe maps to a specific prediction from Phase 3. **Change one variable at a time.**

Tool preference:

1. **Debugger or REPL** where the environment supports it. One breakpoint beats ten logs.
2. **Targeted logs** at the boundaries that distinguish the hypotheses.
3. Never "log everything and grep".

**Tag every debug log** with a unique prefix — `[DEBUG-a4f2]` — so cleanup is a single grep. Untagged logs survive; tagged logs die.

**Multi-component systems:** when data crosses process, service, or environment boundaries, instrument each boundary once — what enters, what exits, what the environment holds — and run it once to see *which* boundary loses it. Then investigate only that component.

**Performance branch:** for regressions, logs are usually the wrong tool. Establish a baseline measurement (timing harness, profiler, query plan), then bisect. Measure first, fix second. `ascension:performance-tuning` covers the measurement toolkit.

## Phase 5: Fix and regression-test

Write the regression test **before the fix**, but only if a **correct seam** exists for it.

A correct seam is one where the test exercises the **real bug pattern as it occurs at the call site**. If the only available seam is too shallow — a single-caller test when the bug needs several, a unit test that cannot replicate the chain that triggered it — a test there gives false confidence.

**If no correct seam exists, that is itself the finding.** Record it: the architecture is preventing the bug from being locked down. `ascension:module-design` names the fix (the module is the wrong shape); raise it rather than pretending the test covers you.

Where a correct seam exists:

1. Turn the minimised repro into a failing test there.
2. Watch it fail.
3. Apply **one** fix, addressing the cause rather than the symptom. No "while I'm here" improvements.
4. Watch it pass.
5. Re-run the Phase 1 loop against the **original, un-minimised** scenario.

### When the fix does not work

Return to Phase 1 with what you learned; do not stack another fix on top. Before the second fix attempt, call the Skill tool with "ascension:error-recovery" and keep its attempt count from then on. After **three** failed fixes, stop and question the architecture instead of attempting a fourth. The tells: each fix reveals new coupling somewhere else, each fix needs "a massive refactor", each fix creates a new symptom. That is a flawed structure, not a failed hypothesis — raise it with the user before continuing.

## Phase 6: Cleanup

Required before declaring done:

- [ ] The original repro no longer reproduces (re-run the Phase 1 loop).
- [ ] The regression test passes, or the absence of a correct seam is written down.
- [ ] All `[DEBUG-...]` instrumentation removed — grep the prefix.
- [ ] Throwaway harnesses deleted, or moved somewhere clearly marked.
- [ ] The hypothesis that turned out correct is stated in the commit or PR message, so the next person to debug this learns from it.

## Supporting methods

In this directory:

- **`root-cause-tracing.md`** — trace a bad value backward through the call stack to its origin.
- **`defense-in-depth.md`** — add validation at multiple layers *after* the root cause is found.
- **`condition-based-waiting.md`** — replace arbitrary timeouts with condition polling.
- **`find-polluter.sh`** — bisect a test suite for the test that pollutes shared state.

**Related:** `ascension:test-first` (writing the regression test), `ascension:completion-gate` (verifying before claiming), `ascension:error-recovery` (when attempts are looping).

---
*The tight-feedback-loop discipline is adapted from [mattpocock/skills](https://github.com/mattpocock/skills) (MIT).*
