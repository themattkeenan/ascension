---
name: domain-modeling
description: Build and sharpen a project's domain language. Use when discussing codebase terminology, when a term is fuzzy or overloaded, when writing or editing CONTEXT.md, or when recording an architectural decision as an ADR.
---

# Domain Modeling

Actively build and sharpen the project's domain model as you design: challenge terms, invent edge-case scenarios, and write the glossary and the decisions down the moment they crystallise.

This is the *active* discipline. Merely **reading** `CONTEXT.md` for vocabulary is a one-line habit any skill can do; this skill is for when you are **changing** the model.

## File layout

Most repos have a single context:

```
/
├── CONTEXT.md
├── docs/adr/
│   ├── 0001-event-sourced-orders.md
│   └── 0002-postgres-for-write-model.md
└── src/
```

If a `CONTEXT-MAP.md` exists at the root, the repo has several contexts and the map points at where each one lives.

Create files **lazily**: write `CONTEXT.md` when the first term is resolved, create `docs/adr/` when the first ADR is needed. Follow the format in [CONTEXT-FORMAT.md](CONTEXT-FORMAT.md) and [ADR-FORMAT.md](ADR-FORMAT.md).

## During the session

**Challenge against the glossary.** When the user uses a term that conflicts with `CONTEXT.md`, say so immediately: *"Your glossary defines 'cancellation' as X, but you seem to mean Y. Which is it?"*

**Sharpen fuzzy language.** When a term is vague or overloaded, propose a precise canonical one: *"You're saying 'account' — do you mean the Customer or the User? Those are different things."*

**Stress-test with concrete scenarios.** Invent specific cases that probe the edges and force precision about where one concept ends and the next begins.

**Cross-reference the code.** When the user states how something works, check whether the code agrees. Surface contradictions: *"Your code cancels whole Orders, but you just said partial cancellation is possible. Which is right?"*

**Update `CONTEXT.md` inline.** When a term resolves, write it down right there. Do not batch.

`CONTEXT.md` is a glossary and nothing else — no implementation detail, no spec, no scratch pad.

## Offer ADRs sparingly

Offer to record a decision only when **all three** are true:

1. **Hard to reverse** — changing your mind later carries real cost.
2. **Surprising without context** — a future reader will wonder *"why on earth did they do it this way?"*
3. **A real trade-off** — there were genuine alternatives and you picked one for specific reasons.

Miss any one and skip the ADR: an easy-to-reverse decision just gets reversed, an unsurprising one prompts no questions, and a decision with no alternative records nothing beyond "we did the obvious thing."

## Where this fits

`ascension:grilling` drives this skill while interviewing, so the vocabulary and the decisions are captured as the conversation produces them. `ascension:knowledge-capture` handles the cross-project lessons; this handles the per-project language.

---
*Adapted from [mattpocock/skills](https://github.com/mattpocock/skills) (MIT).*
