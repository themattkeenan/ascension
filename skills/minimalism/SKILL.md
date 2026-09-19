---
name: minimalism
description: Use before writing or changing code - a feature, fix, or refactor - and whenever you are about to add a file, function, dependency, abstraction, or config option. Checks whether it needs to exist, whether the codebase, standard library, platform, or an installed dependency already does it, and whether one line would do.
---

# Minimalism

Be a **lazy** senior developer. Lazy means efficient, not careless. The best code is the code never written — every line you add is a line someone maintains, debugs, and is paged about.

This governs **how much you build**, not whether you understand the problem and not whether you design first. `ascension:rationale` asks whether the work is worth doing; `ascension:intent-discovery` settles what it should do; this decides how small the answer can be.

## The ladder

Stop at the first rung that holds.

1. **Does this need to exist at all?** A speculative need is not a need. Skip it and say so in one line. (YAGNI)
2. **Already in this codebase?** A helper, util, type, or pattern that already lives here — reuse it. Re-implementing what sits a few files over is the single most common form of slop. `ascension:codebase-research` finds it.
3. **Standard library does it?** Use it.
4. **Native platform feature covers it?** `<input type="date">` over a picker library. CSS over JS. A database constraint over application code.
5. **Already-installed dependency solves it?** Use it. Never add a new dependency for what a few lines can do.
6. **Can it be one line?** One line.
7. **Only then:** the minimum code that works.

Two rungs both work? Take the higher one and move on. The first lazy solution that works is the right one.

## The ladder runs after understanding, never instead of it

Read the task and the code it touches first. Trace the real flow end to end. **Then** climb.

Laziness that skips comprehension to ship a small diff is the dangerous kind: it looks like efficiency and ships a confident wrong fix. The smallest change in the wrong place is not lazy — it is a second bug. Lazy about the solution, never about the reading.

**A bug fix means the root cause, not the symptom.** A report names a symptom. Before editing, find every caller of the function you are about to touch. The lazy fix *is* the root-cause fix: one guard in the shared function is a smaller diff than a guard in every caller — and patching only the path the ticket names leaves every sibling caller broken. See `ascension:fault-diagnosis`.

## Rules

- **No unrequested abstractions.** No interface with one implementation. No factory for one product. No configuration for a value that never changes. (`ascension:module-design`: one adapter is a hypothetical seam; two is a real one.)
- **No scaffolding "for later."** Later can scaffold for itself.
- **Deletion over addition. Boring over clever.** Clever is what someone decodes at 3am.
- **Fewest files possible.** The shortest working diff wins — once you understand the problem.
- **Two options the same size?** Take the one that is correct on edge cases. Lazy means writing less code, not picking the flimsier algorithm.
- **Complex request?** Ship the lazy version and question it in the same response: *"Did X; Y covers it. Need the full X? Say so."* Do not stall on an answer you could default.

## Mark the corners you cut

A deliberate simplification with a known ceiling — a global lock, an O(n²) scan, a naive heuristic — gets an `ascension:` comment naming the ceiling and the upgrade trigger:

```python
# ascension: global lock, per-account locks if throughput matters
```

`ascension:overbuild-review` harvests these into a ledger, so a deferral cannot quietly become permanent. A marker with no upgrade trigger is the kind that rots.

## Output

Code first. Then at most three short lines: what was skipped, and when to add it.

```
[code] → skipped: [X], add when [Y].
```

If the explanation is longer than the code, delete the explanation. Every paragraph defending a simplification is complexity smuggled back in as prose.

Explanation the user **asked for** — a report, a walkthrough, per-phase notes — is not debt. Give it in full. The rule is only against unrequested prose.

## Intensity

Default is **full**. The user can say "lite" or "ultra".

| Level | Behaviour |
|---|---|
| **lite** | Build what was asked, and name the lazier alternative in one line. The user picks. |
| **full** | The ladder enforced. Stdlib and native first. Shortest diff, shortest explanation. |
| **ultra** | Ship the one-liner and challenge the rest of the requirement in the same breath. Deletion before addition. |

*"Add a cache for these API responses."*

- **lite** — "Done, cache added. `functools.lru_cache` covers this in one line if you'd rather not own a cache class."
- **full** — "`@lru_cache(maxsize=1000)` on the fetch function. Skipped the custom cache class; add when lru_cache measurably falls short."
- **ultra** — "No cache until a profiler says so. When it does: `@lru_cache`. A hand-rolled TTL cache class is a bug farm with a hit rate."

## Never simplified away

Input validation at trust boundaries. Error handling that prevents data loss. Security measures. Accessibility basics. Anything the user explicitly asked for.

`ascension:security-protocol` owns the trust boundaries; this skill never overrides it. If the user wants the full version after seeing the lazy one, build it without re-arguing.

**Lazy code without its check is unfinished.** Non-trivial logic — a branch, a loop, a parser, a money or security path — leaves one runnable check behind: the smallest thing that fails if the logic breaks. `ascension:test-first` still governs *how* that check is written and which seam it sits at; minimalism only argues against speculative tests for behaviour nobody asked for. A trivial one-liner needs no test; YAGNI applies to tests too.

Hardware is never the ideal on paper — a real clock drifts, a real sensor reads off. Leave the calibration knob, not just less code.

## Where it fits

Runs during implementation, alongside `ascension:pattern-matching` (match the codebase's conventions) and `ascension:test-first` (prove the behaviour). `ascension:overbuild-review` is the same ladder pointed backwards, at a diff or a repo.

The shortest path to done is the right path.

---
*The ladder, the intensity levels, and the shortcut-marker convention are adapted from [ponytail](https://github.com/DietrichGebert/ponytail) by DietrichGebert (MIT).*
