# Ascension

An AI development framework for coding agents: **49 composable skills** that trigger themselves, spanning discovery, design, planning, execution, and quality.

Ascension began as a merge of god mode's breadth with [Superpowers](https://github.com/obra/superpowers)' subagent-driven depth. v2 adds the mechanisms from [mattpocock/skills](https://github.com/mattpocock/skills), the vocabulary from the [AI Coding Dictionary](https://github.com/mattpocock/dictionary-of-ai-coding), the laziness ladder from [ponytail](https://github.com/DietrichGebert/ponytail), and fourteen engineering books from [agent-rules-books](https://github.com/mattpocock/agent-rules-books) — while pruning the framework back toward what actually changes behaviour.

## How it works

A `SessionStart` hook injects the `activation` skill into every session (and after compaction). That bootstrap makes the agent check for and invoke a relevant skill before *any* response, so the methodology triggers automatically. You do not invoke skills manually; the agent just has them.

## The map

```
rationale → intent-discovery (grilling + reference-engine + prototype)
          → task-planning → work-tickets (when it exceeds one session)
          → implementation (minimalism + test-first + pattern-matching)
          → quality-gate → completion-gate → merge-protocol
```

**On-ramps** merge onto that flow: `fault-diagnosis` when something is broken, `error-recovery` when attempts are looping, `merge-conflicts` mid-rebase, `primary-research` when a decision waits on external facts, `prototype` when a question needs to be seen rather than read, `overbuild-review` when the codebase has drifted into over-engineering, `think-in-code` when a question spans more data than the window should hold.

**Underneath it** sit the reference layers — `vocabulary`, `domain-modeling`, `module-design`, `canon`, `context-economy`, `protocol-authoring` — reached when the *words*, the *shape*, or the *governing principle* are the problem, not the process.

## Installation (Claude Code)

From an interactive `claude` terminal:

```
/plugin marketplace add themattkeenan/ascension
/plugin install ascension
```

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
| `/cut` | Find over-engineering: a diff, the whole repo, or the deferred-shortcut ledger |
| `/canon` | Consult the engineering canon for the decision in front of you |

## What's inside

**Discovery & design** — `rationale`, `grilling`, `intent-discovery` (+ visual companion), `reference-engine`, `canon`, `codebase-research`, `github-search`, `primary-research`, `design-research`, `ux-patterns`, `system-design`, `module-design`, `specification-first`, `prototype`

**Planning & execution** — `task-planning`, `work-tickets`, `delegated-execution`, `task-runner`, `parallel-execution`, `team-orchestration`, `workspace-isolation`, `context-economy`

**Implementation** — `minimalism`, `test-first`, `pattern-matching`, `ui-engineering`, `design-integration`, `project-bootstrap`, `environment-awareness`

**Quality & recovery** — `fault-diagnosis`, `quality-gate`, `overbuild-review`, `quality-enforcement`, `security-protocol`, `completion-gate`, `comprehension-check`, `error-recovery`, `review-response`, `merge-conflicts`, `merge-protocol`, `performance-tuning`

**Meta** — `activation`, `vocabulary`, `domain-modeling`, `protocol-authoring`, `knowledge-capture`, `agent-messaging`, `think-in-code`, `deployment-advisor`

## Some of the mechanisms

A few things Ascension does that are worth knowing about before you use it:

- **A ladder before any code.** `minimalism` stops at the first rung that holds: does this need to exist, is it already in this codebase, does the standard library do it, does the platform do it, does an installed dependency do it, can it be one line. Lazy about the solution, never about the reading — the ladder runs *after* understanding the problem, and never cuts validation, error handling, security, or accessibility.
- **A canon to consult.** `canon` routes to whichever of fourteen engineering books corrects the bias this decision is most likely to get wrong, at nano or mini size, loaded only when reached.
- **Grilling in rounds.** Questions are a **design tree**. The **frontier** is every decision whose prerequisites are settled; the whole frontier is asked in one round, numbered, each with a recommended answer. Facts are the agent's job — it looks them up rather than asking you. Decisions are yours.
- **A tight loop before any theory.** `fault-diagnosis` refuses to hypothesise until one command exists that has already been run and goes **red on this bug** — deterministic, fast, unattended. Ten ways to build one, ranked. Everything after that is mechanical.
- **Seams, agreed up front.** Tests live at seams, and which seams are under test is confirmed before a test is written. `module-design` supplies the vocabulary — module, interface, depth, seam, adapter, leverage, locality — used consistently across design, testing, and review.
- **Two-axis review.** `quality-gate` reviews the diff for **Standards** and for **Spec** in parallel subagents, and reports them separately so one axis cannot mask the other. The Standards axis carries a Fowler smell baseline that applies even where the repo documents nothing.
- **Tracer bullets with blocking edges.** `work-tickets` slices work into vertical slices each sized to one fresh context window, with explicit blockers, and sequences wide refactors as expand–migrate–contract instead of forcing them into a slice.
- **A phase-boundary decision, made deliberately.** `context-economy` puts continue / clear / handoff / subagent / compact in an ordered tree, with the smart zone as the budget you are actually spending.
- **Think in code.** When a question is aggregate — how many, which files, what imports this — `think-in-code` writes the script and prints the answer, instead of pulling the corpus into the window to count by hand.
- **Skills written the way agents read them.** `protocol-authoring` is built on context pointers, the two loads, the information hierarchy, completion criteria, leading words, and the fact that steering by prohibition makes the forbidden behaviour *more* available.

## Credits & license

- **god mode** by David — the original breadth.
- **[Superpowers](https://github.com/obra/superpowers)** by Jesse Vincent / Prime Radiant (MIT) — subagent-driven execution, file handoffs, the visual companion.
- **[mattpocock/skills](https://github.com/mattpocock/skills)** (MIT) — grilling, domain modeling and ADRs, deep modules, the tight feedback loop, two-axis review, tracer-bullet tickets, phase boundaries, and the writing-for-agents discipline underneath `protocol-authoring`.
- **[mattpocock/dictionary-of-ai-coding](https://github.com/mattpocock/dictionary-of-ai-coding)** (MIT) — the shared vocabulary distilled into `vocabulary`.
- **[ponytail](https://github.com/DietrichGebert/ponytail)** by DietrichGebert (MIT) — the laziness ladder, intensity levels, over-build review tags, and the deferred-shortcut ledger.
- **[agent-rules-books](https://github.com/mattpocock/agent-rules-books)** by Maciej Ciemborowicz (MIT) — the fourteen book rule sets behind `canon` (nano and mini editions).
- **[sandcastle](https://github.com/mattpocock/sandcastle)** (MIT) — recommended by `workspace-isolation` for container-level agent isolation.
- **[context-mode](https://github.com/mksglu/context-mode)** by Mert Koseoglu — the think-in-code framing; recommended rather than vendored (ELv2).
- **[claude-mem](https://github.com/thedotmack/claude-mem)** by thedotmack (Apache 2.0) — recommended by `knowledge-capture` for automated cross-session memory.

All MIT. Ascension is a personal merge, MIT. See `LICENSE`.
