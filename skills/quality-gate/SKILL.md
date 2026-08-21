---
name: quality-gate
description: Use when finishing tasks, shipping significant features, or before landing changes to ensure work meets standards through automated review
---

# Quality Gate

Review the diff along **two independent axes**, in parallel subagents, and report them side by side:

- **Standards** — does the code follow this repo's conventions and avoid known smells?
- **Spec** — does the code faithfully implement what the plan, ticket, or spec asked for?

A change can pass one and fail the other: code that follows every convention while implementing the wrong thing, or code that does exactly what was asked while breaking every convention in the repo. Running them together lets one axis mask the other, so they run apart and are reported apart.

## 1. Pin the fixed point

Establish what is being reviewed before spawning anything.

```bash
git rev-parse <fixed-point>          # confirm the ref resolves
git log <fixed-point>..HEAD --oneline # the commits under review
git diff <fixed-point>...HEAD        # three-dot: compare against the merge-base
```

The fixed point is whatever the user names — a SHA, a branch, a tag, `origin/main`, `HEAD~5`. If they did not name one, ask. In a `ascension:delegated-execution` run it is the commit before the task started.

A bad ref or an empty diff should fail here, not inside two parallel subagents.

Also confirm the working tree is clean and the suite is green on this branch. Review of uncommitted work reviews something nobody can reproduce.

## 2. Find the spec source

In this order: the plan or ticket the task came from; a path the user passed; a spec under `docs/plans/`, `docs/`, or `specs/` matching the branch or feature; then ask. If there genuinely is no spec, the Spec axis is skipped and the report says so.

## 3. Find the standards sources

Anything in the repo documenting how code should be written — `CONTRIBUTING.md`, `CODING_STANDARDS.md`, `CLAUDE.md`, `CONTEXT.md`, the ADRs. Skip anything a linter or formatter already enforces; tooling does not need a second opinion.

On top of whatever the repo documents, the Standards axis always carries the **smell baseline** in [code-reviewer.md](code-reviewer.md). Two rules bind it: a documented repo standard always **overrides** the baseline, and every baseline item is a **judgement call** ("possible Feature Envy"), never a hard violation.

## 4. Spawn both subagents in parallel

Fill in [code-reviewer.md](code-reviewer.md) — it contains both briefs. Each subagent gets the diff command, the commit list, and only its own axis's material. Neither sees the other's findings; that is the point.

If no spec exists, spawn only the Standards agent and note the omission.

## 5. Aggregate

Present the two reports under `## Standards` and `## Spec`. Do **not** merge or re-rank across axes — that is exactly the collapse the separation prevents.

End with one line per axis: how many findings, and the worst one *within that axis*. No single winner across the two.

## A third pass, when the diff is large

Standards and Spec both ask "is this right?" Neither asks "is this *too much*?" When the diff is bigger than the change warranted, call the Skill tool with "ascension:overbuild-review" in diff mode — it reports reinvented standard library, needless dependencies, and abstractions with one implementation, and scores the result as lines that could go.

Keep it a separate pass. Folding complexity findings into the Standards axis buries them under conformance issues, which is how over-engineering ships.

## 6. Respond to findings

- **Critical** — resolve now, then re-review.
- **Important** — resolve before advancing to the next task.
- **Minor** — log for a later pass.

Disagreeing with a finding is fine, but object with **evidence** — a test, a line of the spec, a documented standard — not with an assertion. `ascension:review-response` covers working through feedback that is ambiguous, technically suspect, or interdependent.

## When to run it

After each task in a `ascension:delegated-execution` run, after a significant feature, and before landing on the trunk. Also worth running when stuck: an outside read of the diff often unblocks faster than another attempt.

---
*The two-axis structure and smell baseline are adapted from [mattpocock/skills](https://github.com/mattpocock/skills) (MIT).*
