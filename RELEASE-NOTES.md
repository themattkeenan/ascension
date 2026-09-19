# Ascension Release Notes

## v2.1.3 — The bootstrap actually loads

- **Hook commands fixed.** `hooks.json` single-quoted the path — `'${CLAUDE_PLUGIN_ROOT}/hooks/run-hook.cmd'` — and bash does not expand variables inside single quotes, so every hook looked for a file literally named `${CLAUDE_PLUGIN_ROOT}/…`. The failure is non-blocking, so it was silent: across 82 recorded sessions (July 10 onward) the `SessionStart` bootstrap never once injected `activation`, and the v2.1.1 `SubagentStart` hook inherited the same bug. Both now run as `bash "${CLAUDE_PLUGIN_ROOT}/hooks/run-hook.cmd" <script>`, matching every other plugin.
- Consequence for the v2.1.2 analysis: the skill-invocation counts it was based on measured the descriptions alone, with no bootstrap. Re-measure after this release before drawing conclusions about individual skills.

## v2.1.2 — Triggers that fire

A month of transcripts showed 28 of 49 skills never invoked. The skills that did run were the ones another skill hands off to operatively ("call the Skill tool with…"); the rest were only named in passive *Related* lists.

- **Operative handoffs** at the step where they apply: `fault-diagnosis` → `error-recovery` before a second fix attempt; `task-planning` → `context-economy` at the plan → execution boundary; `merge-protocol` gains Phase 6, calling `knowledge-capture` and `context-economy` once work lands.
- **Trigger-first descriptions** for `context-economy`, `minimalism`, `overbuild-review`, `think-in-code`, `test-first`, `quality-gate`, `knowledge-capture`, `canon`, and `comprehension-check` — leading with the condition and naming moments the agent can actually observe (a task just landed, re-reading the same files, the user saying "simplify") in place of identity statements or states it cannot see ("the session is getting long").

## v2.1.1 — Subagent brief

- **`SubagentStart` hook** — `SessionStart` context is parent-thread only, so dispatched subagents ran Ascension-unaware. `general-purpose` and `Plan` subagents now get a short brief: the skills that apply to delegated work, and a boundary that leaves the user-facing skills to the parent. Deliberately not the full `activation` protocol, which is written for the parent session. Scoped with the native agent-type matcher in `hooks/hooks.json`. Idea from [ponytail](https://github.com/DietrichGebert/ponytail)'s subagent hook (MIT).

## v2.1.0 — Restraint and canon

v2.0 made the framework better at *doing the work*. v2.1 makes it better at **not doing work that shouldn't be done** — and gives it a library of engineering principle to argue from.

45 skills → **49**.

### New skills

- **`minimalism`** — the laziness ladder, adapted from [ponytail](https://github.com/DietrichGebert/ponytail) (MIT). Before writing code, stop at the first rung that holds: does this need to exist → is it already in this codebase → does the standard library do it → does the platform do it → does an installed dependency do it → can it be one line → the minimum that works. Three intensity levels (`lite` / `full` / `ultra`), a `[code] → skipped: X, add when Y` output pattern, and a hard floor: validation at trust boundaries, error handling that prevents data loss, security, and accessibility are never on the chopping block.

  The ladder runs **after** understanding the problem, never instead of it. Ponytail's own benchmark (12 tasks against a real FastAPI + React repo, n=4, Haiku 4.5) reports −54% lines, −22% tokens, −27% time versus the same agent with no skill, at 100% safety — the only arm in that study to cut every metric while staying fully safe.

- **`overbuild-review`** — the same ladder pointed backwards, in three modes. **diff** (review a change), **repo** (audit the tree), **debt** (harvest deferred-shortcut markers into a ledger). Five tags — `delete:`, `stdlib:`, `native:`, `yagni:`, `shrink:` — one line per finding with location, cut, and replacement, scored as `net: -N lines possible`. Explicitly scoped to complexity only; correctness and security route to `quality-gate` and `security-protocol`.

- **`canon`** — fourteen software-engineering books distilled into applicable rule sets, vendored from [agent-rules-books](https://github.com/mattpocock/agent-rules-books) (MIT). A Philosophy of Software Design, Clean Architecture, Clean Code, Code Complete, DDIA, DDD (×3), PoEAA, Refactoring (×2), Release It!, The Pragmatic Programmer, Working Effectively with Legacy Code.

  Routed by **which bias the decision is most likely to get wrong**, not by title. Two sizes per book (nano ~15-25 rules, mini ~30-45), both disclosed reference — zero context cost until reached. The skill states plainly that the books conflict on purpose, that the project's `CONTEXT.md` and ADRs override any canonical rule, and that one or two books beats loading the shelf.

- **`think-in-code`** — compute the answer with a script and print only the result, instead of reading the corpus into the window. The framing is adapted from [context-mode](https://github.com/mksglu/context-mode); the skill is written from scratch because context-mode is ELv2, not MIT. Includes the test for when it applies (aggregate/mechanical questions) and when it does not (understanding, which is what the window is for).

### New shortcut markers

Deliberate simplifications with a known ceiling now carry an `ascension:` comment naming the ceiling and the upgrade trigger:

```python
# ascension: global lock, per-account locks if throughput matters
```

`/cut debt` harvests them into a ledger and flags any marker with no upgrade trigger — those are the ones that rot into permanent.

### Wiring

- `reference-engine` now splits references into **implementation** ("what does a good one look like?") and **principle** ("what rule governs this?"), routing the second to `canon`.
- `quality-gate` gains an optional third pass: `overbuild-review` in diff mode, kept separate so complexity findings are not buried under conformance ones.
- `codebase-research` routes aggregate survey questions to `think-in-code`.
- `pattern-matching` gains rung 2 of the ladder — the most common slop is re-implementing what lives a few files over.
- `knowledge-capture` mentions [claude-mem](https://github.com/thedotmack/claude-mem) (Apache 2.0) for automated cross-session memory, while keeping the judgement about what is worth keeping.
- `activation`'s map, ordering, and on-ramp table updated; `vocabulary` gains *the ladder*, *lazy*, *over-build*, and *think in code*.

### New slash commands

`/lazy [lite|full|ultra]`, `/cut [diff|repo|debt]`, `/canon`.

### Conflicts resolved explicitly

`minimalism` and `test-first` disagree at the margin, so the boundary is stated in both: minimalism argues against *speculative* tests, never against the seam discipline or the red-green loop. `minimalism` and `security-protocol` never trade off — the security floor wins. `canon`'s books contradict each other, and the skill says so rather than averaging them.

## v2.0.0 — Mechanisms over mandates

v2 adds a third lineage: the mechanisms from [mattpocock/skills](https://github.com/mattpocock/skills) and the vocabulary from the [AI Coding Dictionary](https://github.com/mattpocock/dictionary-of-ai-coding), both MIT. The theme is replacing exhortation with machinery — where v1 told the agent to be thorough, v2 gives it a procedure whose completion criterion it cannot fake.

36 skills → **45**.

### New skills

- **`grilling`** — the interview primitive. Questions form a **design tree**; the **frontier** is every decision whose prerequisites are settled, and the whole frontier is asked in one numbered round with a recommended answer attached to each. Facts are the agent's job (dispatch a subagent); decisions are the user's. Done when the frontier is empty.
- **`domain-modeling`** — `CONTEXT.md` as a glossary and `docs/adr/` as the decision record, both created lazily. Challenge terms against the glossary, sharpen overloaded words, cross-reference the code, write it down inline. ADRs offered only when a decision is hard to reverse **and** surprising **and** a real trade-off.
- **`module-design`** — deep-module vocabulary: module, interface, depth, seam, adapter, leverage, locality. Includes `DEEPENING.md` (dependency categories, seam discipline, replace-don't-layer testing) and `DESIGN-IT-TWICE.md` (parallel subagents designing the same interface under different constraints).
- **`context-economy`** — the phase-boundary decision as an ordered tree: continue → clear → handoff → subagent → compact, with compact as the *default rather than the first reach*. Carries the smart zone / dumb zone model and the handoff document format.
- **`prototype`** — throwaway code that answers one question. Logic branch versus UI branch, six rules, and capture-as-primary-source on a `prototype/<name>` branch when done.
- **`primary-research`** — investigate against primary sources via a background agent, cite every claim, and treat uncited facts as leads rather than findings.
- **`work-tickets`** — tracer-bullet vertical slices with explicit blocking edges, each sized to one fresh context window. Wide refactors sequence as expand–migrate–contract instead of being forced into a slice.
- **`merge-conflicts`** — resolve by tracing each side's intent to its primary source. Always resolve, never `--abort`.
- **`vocabulary`** — the shared leading words the whole framework thinks with, so skills, plans, and reports speak one language.

### Rewritten skills

- **`fault-diagnosis`** — rebuilt around a six-phase discipline whose Phase 1 *is* the skill: build a **tight feedback loop** before forming any theory. Ten ranked ways to construct one, an explicit completion criterion (one command, already run, red-capable, deterministic, fast, unattended), reproduce-and-minimise, 3–5 ranked falsifiable hypotheses shown to the user before testing, tagged `[DEBUG-xxxx]` instrumentation, and the "no correct seam is itself the finding" rule. Secret redaction is now a precondition. Fabricated success statistics removed.
- **`test-first`** — adds **seams** as the unit of testing and requires them to be agreed with the user before any test is written. Adds the tautological-test anti-pattern, the vertical-slice/tracer-bullet rule against horizontal slicing, and boundaries-only mocking guidance. Cut from 372 lines to ~200 by removing seven restatements of the same rule.
- **`quality-gate`** + **`code-reviewer`** — now a **two-axis** review: Standards and Spec run in parallel subagents and report separately, so neither can mask the other. The Standards axis carries a twelve-item Fowler smell baseline that applies even where the repo documents nothing, overridden by any documented repo standard.
- **`protocol-authoring`** — rebuilt on the writing-for-agents discipline: context pointers, the two loads (context vs cognitive), the information hierarchy, completion criteria and premature completion, leading words, and the finding that **steering by prohibition makes the forbidden behaviour more available**. Adds a no-op test, a ban on invented metrics, and a RED/GREEN baseline validation step before deploying a skill.
- **`intent-discovery`** — replaces one-question-at-a-time with grilling rounds, routes runnable questions to `prototype`, and requires the design to name the seams tests will live at.
- **`activation`** — pruned and given a **map**: the main flow, the on-ramps, and the vocabulary layers underneath. The twelve-row rationalization table is gone (it was a script of excuses in the agent's own voice); guardrails are phrased as positive targets.

### Wiring

- `system-design` → `module-design` and `domain-modeling` for interface shape and naming.
- `task-planning` → `work-tickets` when the plan exceeds one session; `context-economy` at the plan/execute boundary.
- `knowledge-capture` → `domain-modeling`, with an explicit split: cross-project lessons go to memory, project facts go to `CONTEXT.md` and ADRs.
- `agent-messaging` → `context-economy` before writing any handoff.
- `merge-protocol` → `merge-conflicts` when a merge stops.
- `workspace-isolation` → a container sandbox ([sandcastle](https://github.com/mattpocock/sandcastle)) when a worktree's filesystem-and-branch isolation is not enough.

### New slash commands

`/grill`, `/tickets`, `/review`, `/handoff`. `/ascension` now locates the task on the map before recommending an execution strategy.

### Removed

Fabricated performance statistics, and most of the rationalization tables. Both were teaching the wrong thing: unsourced numbers read as authority, and a long list of excuses phrased in the agent's own voice makes them more available, not less.

## v1.0.0 — Initial merge

Ascension is a merge of two frameworks: **god mode** (base, 36 skills of breadth) and **Superpowers** (depth). This first release establishes the combined baseline.

### Inherited from god mode (base)
- All 36 skills, the `activation` bootstrap, cross-platform hook wrapper (`run-hook.cmd`), slash commands, and the `code-reviewer` agent.

### Grafted in from Superpowers (depth)
- **`delegated-execution` overhaul.** Added the `sdd-workspace`, `task-brief`, and `review-package` scripts. Rewrote the SKILL to use file handoffs (briefs, report files, review packages) instead of pasting task text into the controller context; added Model Selection, Pre-Flight Plan Review, Handling Builder Status (`DONE`/`DONE_WITH_CONCERNS`/`BLOCKED`/`NEEDS_CONTEXT`), Constructing Auditor Prompts (no pre-judging findings), and a compaction-safe **Durable Progress** ledger. Upgraded the builder prompt and both auditor prompts to read files.
- **Visual brainstorming companion** ported into `intent-discovery/scripts/` (zero-dependency node server + client). Rebranded to "Ascension Visual Brainstorming"; **all telemetry / external image loads removed** — the server makes no network requests. Session files persist under `<project>/.ascension/brainstorm/`.
- **Document reviewer prompts:** `intent-discovery/spec-document-reviewer-prompt.md` (design docs) and `task-planning/plan-document-reviewer-prompt.md` (implementation plans).
- **`persuasion-principles.md`** added to `protocol-authoring` and referenced from its rationalization-hardening section.

### Rebranding
- `godmode` → `ascension` across all skills, hooks, manifests, and commands. Skill namespace is now `ascension:`. Scratch directories are `.ascension/sdd` and `.ascension/brainstorm`. The `/godmode` command is now `/ascension`.

### Notes
- Claude Code focused: the god mode `.codex` / `.cursor-plugin` / `.opencode` harness adapters and the marketing `site/` were intentionally left out of this build.
