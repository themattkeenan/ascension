# Ascension

An AI development framework for coding agents: **29 composable skills** behind four always-on working rules, spanning discovery, design, planning, execution, and quality.

Ascension began as a merge of god mode's breadth with [Superpowers](https://github.com/obra/superpowers)' subagent-driven depth. v2 adds the mechanisms from [mattpocock/skills](https://github.com/mattpocock/skills), the vocabulary from the [AI Coding Dictionary](https://github.com/mattpocock/dictionary-of-ai-coding), the laziness ladder from [ponytail](https://github.com/DietrichGebert/ponytail), and fourteen engineering books from [agent-rules-books](https://github.com/mattpocock/agent-rules-books) — while pruning the framework back toward what actually changes behaviour.

## How it works

A `SessionStart` hook injects the body of the `activation` skill into every session (and after compaction): four working rules, a table mapping situations to skills, and a short set of reply rules. Small tasks need no skill; when a situation in the table comes up, the agent loads the skill for it. v2.3 replaced the earlier "invoke a skill before any response" mandate, which was followed in about one session in five.

Session-start context never reaches subagents, so a `SubagentStart` hook gives `general-purpose` and `Plan` subagents a short brief of their own: which skills apply to delegated work, and that the user-facing ones (`rationale`, `grilling`, `intent-discovery`, `merge-protocol`) belong to the parent session. To change which agents receive it, edit the `matcher` in `hooks/hooks.json`.

## The map

```
rationale → intent-discovery (grilling + reference-engine + prototype)
          → task-planning → work-tickets (when it exceeds one session)
          → implementation (minimalism + test-first + pattern-matching)
          → quality-gate → completion-gate → merge-protocol
```

**On-ramps** merge onto that flow: `fault-diagnosis` when something is broken, `merge-conflicts` mid-rebase, `prototype` when a question needs to be seen rather than read.

**Underneath it** sit `domain-modeling`, `context-economy`, and `protocol-authoring`, reached when the *words*, the session itself, or the skills are the problem, not the process.

## Installation (Claude Code)

From an interactive `claude` terminal, one step on Claude Code 2.1.275 or later:

```
/plugin install ascension --marketplace themattkeenan/ascension
```

Or in two, on older versions:

```
/plugin marketplace add themattkeenan/ascension
/plugin install ascension
```

In the Claude desktop app, the same marketplace appears as `ascension` under Plugins; install or update it there.

Then restart the session so the `SessionStart` hook loads.

> **Avoid dueling bootstraps.** Ascension already contains god mode and Superpowers. If you previously installed either, disable them (`/plugin`) so only one bootstrap hook runs per session.

## Slash commands

| Command | What it does |
|---|---|
| `/ascension` | Locate the task on the map, recommend an execution strategy, invoke the relevant skills |
| `/brainstorm` | Intent discovery — grill, reference, design, approve |
| `/grill` | Relentless interview in rounds until every open decision is settled |
| `/write-plan` | Turn an approved design into an implementation plan |
| `/tickets` | Slice a plan or spec into tracer-bullet tickets with blocking edges |
| `/execute-plan` | Execute a plan in batches with review checkpoints |
| `/review` | Two-axis review of the diff — Standards and Spec, in parallel |
| `/handoff` | Decide what to do at this phase boundary, and write the handoff if one is needed |
| `/lazy` | Lazy senior dev mode — climb the ladder before writing code (`lite` / `full` / `ultra`) |

## What's inside

**Discovery & design** — `rationale`, `grilling`, `intent-discovery` (+ visual companion), `reference-engine`, `codebase-research`, `design-research`, `ux-patterns`, `specification-first`, `prototype`

**Planning & execution** — `task-planning`, `work-tickets`, `delegated-execution`, `task-runner`, `workspace-isolation`, `context-economy`

**Implementation** — `minimalism`, `test-first`, `pattern-matching`, `ui-engineering`

**Quality & recovery** — `fault-diagnosis`, `quality-gate`, `security-protocol`, `completion-gate`, `merge-conflicts`, `merge-protocol`

**Meta** — `activation`, `domain-modeling`, `protocol-authoring`, `agent-messaging`

Twenty skills were removed in v2.3 after a usage count; they are in git history at tag `v2.2.0` (see `RELEASE-NOTES.md`).

## Some of the mechanisms

A few things Ascension does that are worth knowing about before you use it:

- **A ladder before any code.** `minimalism` stops at the first rung that holds: does this need to exist, is it already in this codebase, does the standard library do it, does the platform do it, does an installed dependency do it, can it be one line. Lazy about the solution, never about the reading — the ladder runs *after* understanding the problem, and never cuts validation, error handling, security, or accessibility.
- **Grilling in rounds.** Questions are a **design tree**. The **frontier** is every decision whose prerequisites are settled; the whole frontier is asked in one round, numbered, each with a recommended answer. Facts are the agent's job — it looks them up rather than asking you. Decisions are yours.
- **A tight loop before any theory.** `fault-diagnosis` refuses to hypothesise until one command exists that has already been run and goes **red on this bug** — deterministic, fast, unattended. Ten ways to build one, ranked. Everything after that is mechanical.
- **Seams, agreed up front.** Tests live at seams, and which seams are under test is confirmed before a test is written.
- **Two-axis review.** `quality-gate` reviews the diff for **Standards** and for **Spec** in parallel subagents, and reports them separately so one axis cannot mask the other. The Standards axis carries a Fowler smell baseline that applies even where the repo documents nothing.
- **Tracer bullets with blocking edges.** `work-tickets` slices work into vertical slices each sized to one fresh context window, with explicit blockers, and sequences wide refactors as expand–migrate–contract instead of forcing them into a slice.
- **A phase-boundary decision, made deliberately.** `context-economy` puts continue / clear / handoff / subagent / compact in an ordered tree, with the smart zone as the budget you are actually spending.
- **Skills written the way agents read them.** `protocol-authoring` is built on context pointers, the two loads, the information hierarchy, completion criteria, leading words, and the fact that steering by prohibition makes the forbidden behaviour *more* available.

## Credits & license

- **god mode** by David — the original breadth.
- **[Superpowers](https://github.com/obra/superpowers)** by Jesse Vincent / Prime Radiant (MIT) — subagent-driven execution, file handoffs, the visual companion.
- **[mattpocock/skills](https://github.com/mattpocock/skills)** (MIT) — grilling, domain modeling and ADRs, deep modules, the tight feedback loop, two-axis review, tracer-bullet tickets, phase boundaries, and the writing-for-agents discipline underneath `protocol-authoring`.
- **[mattpocock/dictionary-of-ai-coding](https://github.com/mattpocock/dictionary-of-ai-coding)** (MIT) — the shared vocabulary the skills are written in.
- **[ponytail](https://github.com/DietrichGebert/ponytail)** by DietrichGebert (MIT) — the laziness ladder, intensity levels, over-build review tags, and the deferred-shortcut ledger.
- **[sandcastle](https://github.com/mattpocock/sandcastle)** (MIT) — recommended by `workspace-isolation` for container-level agent isolation.
- **[andrej-karpathy-skills](https://github.com/multica-ai/andrej-karpathy-skills)**: the shape of the four always-on working rules in `activation`.
- **[i-have-adhd](https://github.com/ayghri/i-have-adhd)** by ayghri (MIT): the reply rules in `activation`.

All MIT. Ascension is a personal merge, MIT. See `LICENSE`.
