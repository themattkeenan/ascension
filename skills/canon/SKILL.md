---
name: canon
description: Use when a design or review decision needs a governing principle rather than an opinion - architecture boundaries, refactoring, legacy code, domain modeling, data correctness, operational resilience, or construction quality. Routes to the engineering book whose rules bear on it.
---

# Canon

Fourteen classic engineering books, distilled into rule sets an agent can apply. Each answers one question: **what bias does this decision need corrected?**

Every book file is disclosed reference — it costs nothing until you read it. Read one when its bias applies, not by default.

## Choosing

Match the **decision in front of you** to the bias it is most likely to get wrong.

| The risk right now | Book | File |
|---|---|---|
| Accidental complexity, shallow abstractions, leaky interfaces, tactical patches | A Philosophy of Software Design | `a-philosophy-of-software-design` |
| Framework-first design, database-shaped policy, layer bypass, fake boundaries | Clean Architecture | `clean-architecture` |
| Unreadable, surprising code; naming and function shape | Clean Code | `clean-code` |
| Defect risk during construction; review and debugging discipline | Code Complete | `code-complete` |
| Data correctness, durability, distributed write semantics, hidden data contracts | Designing Data-Intensive Applications | `designing-data-intensive-applications` |
| Business language, context ownership, invariants, integration meaning | Domain-Driven Design | `domain-driven-design` |
| The same, in compact form — start here for DDD | DDD Distilled | `domain-driven-design-distilled` |
| Applying DDD tactically in a real codebase | Implementing DDD | `implementing-domain-driven-design` |
| Persistence, transactions, remote boundaries, pattern choice | Patterns of Enterprise Application Architecture | `patterns-of-enterprise-application-architecture` |
| Cleanup drifting into rewrite or hidden behaviour change | Refactoring | `refactoring` |
| Smell-driven refactoring, compact always-on bias | Refactoring Guru | `refactoring-guru` |
| Production failure semantics, overload, isolation, recovery, diagnosability | Release It! | `release-it` |
| Accountability, adaptability, fast feedback; system-level consequences of local edits | The Pragmatic Programmer | `the-pragmatic-programmer` |
| Changing poorly tested or poorly understood code; getting it under control first | Working Effectively with Legacy Code | `working-effectively-with-legacy-code` |

## Reading one

Two sizes per book, in `books/`:

- **`books/<slug>.nano.md`** — ~15-25 rules. The default. Enough to steer a decision.
- **`books/<slug>.mini.md`** — ~30-45 rules. When the nano version is too coarse for the call you are making, or when the book *is* the topic.

Each file has the same shape: **When to use** → **Primary bias to correct** → **Decision rules** → **Trigger rules** → **Final checklist**. Read the bias line first; if it does not describe your risk, you picked the wrong book.

The full versions are not vendored here — [mattpocock/agent-rules-books](https://github.com/mattpocock/agent-rules-books) has them.

## Rules of use

- **One or two books, not the shelf.** Loading five rule sets produces a decision that satisfies none of them. Pick the book whose bias matches your actual risk.
- **The project overrides the book.** A documented convention, an ADR, or an explicit user instruction wins over a canonical rule every time. `CONTEXT.md` and `docs/adr/` are the local law; these are the background law.
- **They conflict, deliberately.** Clean Architecture wants boundaries; A Philosophy of Software Design is suspicious of layers that hide no complexity; `ascension:minimalism` wants neither until something varies. When two rule sets disagree, say so and pick — do not average them into mush.
- **These are heuristics with authority, not proofs.** A rule that does not fit the case does not become right by being canonical.

## Where it fits

`ascension:reference-engine` routes here when a decision needs a canonical rule set. `ascension:module-design` supplies Ascension's own interface vocabulary and takes precedence over any book's competing terms — the point of a fixed vocabulary is that it stays fixed.

Reach for a book when the *principle* is the question. Reach for `ascension:github-search` or `ascension:codebase-research` when a *working implementation* is the question.

---
*Rule sets vendored from [mattpocock/agent-rules-books](https://github.com/mattpocock/agent-rules-books) by Maciej Ciemborowicz (MIT). Nano and mini editions only.*
