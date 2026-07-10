# Ascension

A merged AI development framework for coding agents. **Ascension takes god mode's breadth and fuses it with Superpowers' depth** — one coherent, self-triggering methodology.

- **Base (breadth):** god mode — 36 composable skills spanning intent discovery, reference-first design, spec-driven planning, UX/security/architecture, agent orchestration, and automated quality gates.
- **Depth (grafted in from [Superpowers](https://github.com/obra/superpowers)):**
  - **Subagent-driven execution done right** — `delegated-execution` now hands work to subagents as *files* (task briefs, report files, review packages) instead of pasting task text into the controller's context, uses explicit builder status codes (`DONE` / `DONE_WITH_CONCERNS` / `BLOCKED` / `NEEDS_CONTEXT`), and keeps a **compaction-safe progress ledger** so a controller never re-dispatches completed tasks after a context reset. Includes the `sdd-workspace`, `task-brief`, and `review-package` scripts.
  - **Visual brainstorming companion** — a zero-dependency local server that renders mockups, diagrams, and clickable options in the browser during `intent-discovery`. Makes **no network requests** (all telemetry removed).
  - **Document reviewers** — dispatchable reviewer prompts for the design doc (`intent-discovery`) and the implementation plan (`task-planning`).
  - **Skill-authoring reference** — Superpowers' `persuasion-principles.md` wired into `protocol-authoring`.

## How it works

A `SessionStart` hook injects the `activation` skill into every session (and after compaction). That bootstrap makes the agent check for and invoke a relevant skill before *any* response — so the methodology triggers automatically. You don't invoke skills manually; the agent just has them.

The core loop: **rationale → intent-discovery → reference-engine → design → task-planning → delegated-execution (build + two-stage review per task) → quality gates → merge-protocol.**

## Installation (Claude Code)

From an interactive `claude` terminal:

```
/plugin marketplace add themattkeenan/ascension
/plugin install ascension
```

Then restart the session so the `SessionStart` hook loads.

> **Important — avoid dueling bootstraps.** Ascension already contains god mode and Superpowers. If you previously installed either, **disable them** (`/plugin`) so only one `activation`/bootstrap hook runs per session. Two competing bootstraps fight for control at session start.

## What's inside

**36 skills**, including:

- **Discovery & design:** `rationale`, `intent-discovery` (+ visual companion), `reference-engine`, `codebase-research`, `github-search`, `design-research`, `ux-patterns`, `system-design`, `specification-first`
- **Planning & execution:** `task-planning`, `delegated-execution`, `task-runner`, `parallel-execution`, `team-orchestration`, `workspace-isolation`
- **Implementation:** `test-first`, `pattern-matching`, `ui-engineering`, `design-integration`, `project-bootstrap`, `environment-awareness`
- **Quality & recovery:** `fault-diagnosis`, `quality-gate`, `quality-enforcement`, `security-protocol`, `completion-gate`, `comprehension-check`, `error-recovery`, `review-response`, `merge-protocol`
- **Meta:** `activation`, `protocol-authoring`, `knowledge-capture`, `agent-messaging`, `deployment-advisor`

Slash commands: `/ascension`, `/brainstorm`, `/write-plan`, `/execute-plan`.

## Credits & license

- **god mode** by David — the base framework.
- **Superpowers** by Jesse Vincent / Prime Radiant (MIT) — the depth grafted in.

Both are MIT-licensed; ascension is a personal merge, MIT. See `LICENSE`.
