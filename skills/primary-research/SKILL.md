---
name: primary-research
description: Use when a decision waits on external facts — third-party API behaviour, spec details, library internals, version differences — or when reading legwork should be delegated to a background agent. Investigates against primary sources and leaves cited findings as a file in the repo.
---

# Primary Research

Delegate reading legwork so you keep working while it happens.

## What counts as a primary source

The thing itself: official documentation, the library's actual source, the RFC or spec, the first-party API response, the changelog. Not a blog post *about* it, not a Stack Overflow answer summarising it, not your own recollection.

A secondary source was accurate when written and has been on its own schedule ever since. When a claim matters, follow it back to the source that owns it.

## The loop

1. **Sharpen the question first.** "Research auth" produces a book. "Does this provider's refresh token rotate on use, and what happens to the old one?" produces an answer. Write the question down before dispatching.
2. **Dispatch a background agent** with that question. Its brief:
   - Investigate against **primary sources**; follow every claim back to the source that owns it.
   - Quote or link the exact source for each claim — file and line for source code, section anchor for docs, version for anything versioned.
   - Say plainly what it **could not** determine, rather than filling gaps with plausible inference.
   - Write the findings to a single Markdown file.
3. **Do not block.** Only the questions downstream of the research wait for it; keep working on everything else.
4. **File the findings** where the repo already keeps such notes. Match the existing convention; if there is none, put it somewhere sensible and say where.

## Reading the result

Facts with a citation are usable. Facts without one are the agent's parametric knowledge, which has a **knowledge cutoff** and does not know what shipped last month — treat them as leads to verify, not findings.

Research feeds the thinking rather than replacing it: take the file into `ascension:grilling` or `ascension:intent-discovery`, do not hand it straight to an implementer.

## Neighbours

- `ascension:github-search` — finding existing implementations to study or use.
- `ascension:codebase-research` — the same discipline pointed at *this* repo.
- `ascension:design-research` — visual and layout references.

---
*Adapted from [mattpocock/skills](https://github.com/mattpocock/skills) (MIT).*
