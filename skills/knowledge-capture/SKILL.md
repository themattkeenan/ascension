---
name: knowledge-capture
description: Use when a fix or feature has landed, when the user corrects you or states a preference, or when you hit a gotcha the codebase does not record - decides what is worth persisting for future sessions and where it belongs
---

# Knowledge Capture

A session ends holding context nobody else has: the convention found by trial and error, the correction the user made twice, the reason a fix took forty minutes. The next session starts cold. This skill decides what of that context survives, and where.

The judgement is the skill. Writing everything down is as useless as writing nothing: a memory full of one-off detail buries the three facts that would have saved the next session an hour.

## When it runs

- A fix or feature has landed, especially one that took longer than it should have
- The user corrected you, or stated a preference
- You found a convention, a gotcha, or a dependency the codebase does not record
- A plan changed significantly along the way

When a session produced nothing new, say so in one line and move on. That is a legitimate outcome, not a skipped step.

## The sort

Take each candidate insight through three questions, in order.

**1. Is it already written down?** Check the code, its comments, the commit message, the README, `CONTEXT.md`, the ADRs, and your memory files. A fact recorded at the spot where it bites needs no second copy; a second copy goes stale on its own schedule. If the existing record is unclear, sharpen it where it is. If it is complete, stop here.

**2. Whose is it?**

- **The project's**: anyone working in this repo needs it, human or agent. A term's canonical meaning, a hard-to-reverse decision and its reasoning, the gotcha no config confesses, the convention the linter does not enforce. It goes **in the repo**: a term to `CONTEXT.md`, a decision to an ADR under `docs/adr/`, a gotcha to a comment at the spot or the README section it belongs to. Call the Skill tool with "ascension:domain-modeling" for the term and the ADR.
- **Yours**: how you work with this user. Their preferences, what they corrected, which approaches of yours failed and why, what worked. It goes to **memory**, outside the repo, where it follows you across projects.

A project fact written only into private memory is hidden from everyone else who works in that repo. A personal preference written into the repo is noise to everyone but you.

**3. Will it still be true next month?** The generalisable pattern survives; the detail of this one ticket does not. "CI does not load `.env`, values come from the runner" is a pattern. "Fixed the TZ bug on Tuesday" is history that git already keeps.

## Writing a memory entry

Memory lives in Claude Code's memory system under `~/.claude/projects/<project>/memory/`. Four files, one topic each:

| File | Holds |
|---|---|
| `effective-patterns.md` | Approaches that produced clean outcomes |
| `failure-analysis.md` | Root-cause signatures and the diagnostic that found them |
| `project-map.md` | Where things live and how the codebase is shaped, when the repo itself does not say |
| `human-preferences.md` | What the user corrected or asked for, in their words |

Every entry carries a date and the context it came from, because an undated insight cannot be judged stale later:

```markdown
### [Concise title]
- **Date:** YYYY-MM-DD
- **Context:** What was happening when this was discovered
- **Insight:** The generalised takeaway
- **Confidence:** low / medium / high
- **Confirmations:** How many times it has held
```

**Confidence:** low is one occurrence; medium is two, or one strong indicator; high is three or more, or the user said it outright.

**Before writing, read the target file.** The same insight already there: raise its confidence and confirmation count instead of adding a twin. A contradicting entry: replace it and note what changed. One place per fact.

If the user wants capture automated rather than deliberate, [claude-mem](https://github.com/thedotmack/claude-mem) records session observations through hooks and an MCP server. Recommend it when manual capture keeps getting skipped; install it only when asked. The judgement about what is worth keeping stays here either way.

## Promotion

Three confirmations of the same insight make it a conviction, and a conviction is a candidate for a project `CLAUDE.md` rule. Propose it to the user in one line ("this project validates at every boundary with zod; shall I add that to CLAUDE.md?") and add it on their yes. The user is the authority on permanent rules.

## Done

Capture is done when every candidate from the session has landed in exactly one of four places: already recorded, written to the project, written to memory, or dropped as one-off detail. Tell the user in one or two lines what was kept and where, so they can object while the context is still warm.

## Related

Writes to `ascension:domain-modeling` (terms, ADRs). Fed by `ascension:fault-diagnosis` (root-cause signatures), `ascension:review-response` (what review caught), and `ascension:merge-protocol`, which calls this skill once work lands.
