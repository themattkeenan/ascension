# Ascension Release Notes

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
