---
name: work-tickets
description: Break a spec, plan, or conversation into tracer-bullet tickets that each declare their blocking edges. Use when work is too big for one session, when parallel or resumable execution is wanted, or when a plan needs slicing into independently shippable pieces.
---

# Work Tickets

Break the work into **tickets**: tracer-bullet vertical slices, each declaring the tickets that **block** it.

A ticket is sized to fit one fresh context window, start to finish. That is the whole constraint behind the shape.

## 1. Gather context

Work from what is already in the conversation. If the user passes a reference (a spec path, an issue number or URL), fetch it and read the full body and comments.

## 2. Read the codebase

Understand the current state before slicing. Ticket titles and bodies use the project's domain vocabulary (`CONTEXT.md`) and respect the ADRs covering the area.

Look for **prefactoring** opportunities: make the change easy, then make the easy change. Prefactors go first, as their own tickets.

## 3. Draft the slices

- Each slice cuts a **narrow but complete** path through every layer — schema, API, UI, tests. Vertical, never a horizontal slice of one layer.
- A completed slice is demoable or verifiable on its own.
- Each slice fits in a single fresh context window.
- Each slice declares its **blocking edges**: the tickets that must complete before it can start. No blockers means it can start immediately.

### Wide refactors are the exception

A **wide refactor** is one mechanical change — rename a column, retype a shared symbol — whose blast radius fans across the codebase, so a single edit breaks thousands of call sites and no vertical slice can land green. Do not force it into a tracer bullet. Sequence it **expand–contract**:

1. **Expand** — add the new form beside the old so nothing breaks.
2. **Migrate** — move call sites over in batches sized by blast radius (per package, per directory), each batch its own ticket blocked by the expand. CI stays green batch to batch because the old form still exists.
3. **Contract** — delete the old form once no caller remains, in a ticket blocked by every migrate batch.

When even the batches cannot stay green alone, keep the sequence but let them share an integration branch that all block a final integrate-and-verify ticket; green is promised only there.

## 4. Quiz the user

Present the breakdown as a numbered list. Per ticket: **title**, **blocked by**, **what it delivers** (the end-to-end behaviour it makes work).

Then ask:

- Does the granularity feel right — too coarse, too fine?
- Are the blocking edges correct, or is something listed as a blocker that does not actually gate it?
- Should any be merged or split?

Iterate until the user approves.

## 5. Publish

Write one file per ticket to `docs/plans/<feature-slug>/tickets/<NN>-<slug>.md`, numbered from `01` in dependency order — blockers first. One ticket per file, never one combined file.

If the project uses a real issue tracker and the user wants it there, publish one issue per ticket in dependency order and use the platform's native blocking or sub-issue relationship where it has one.

```markdown
# <NN>: <Ticket title>

**What to build:** the end-to-end behaviour this ticket makes work, from the
user's perspective — not a layer-by-layer implementation list.

**Blocked by:** the numbers/titles that gate this one, or "None (can start immediately)".

**Status:** ready

- [ ] Acceptance criterion 1
- [ ] Acceptance criterion 2
```

Avoid file paths and code snippets in tickets — they go stale fast. The exception: a snippet that encodes a decision more precisely than prose can (a state machine, a reducer, a schema, a type shape), trimmed to the decision-rich part.

## 6. Work the frontier

The **frontier** is every ticket whose blockers are all done. Any frontier ticket can be grabbed. For a purely linear chain that means top to bottom.

Start each ticket in a **fresh context** — the ticket is self-contained by construction, so the previous one's context is disposable (`ascension:context-economy`). Independent frontier tickets can run in parallel via `ascension:parallel-execution` or `ascension:delegated-execution`.

## Neighbours

`ascension:task-planning` produces the plan; this skill slices it into independently executable units. `ascension:specification-first` produces the spec this slices from.

---
*Adapted from [mattpocock/skills](https://github.com/mattpocock/skills) (MIT).*
