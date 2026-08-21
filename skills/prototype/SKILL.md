---
name: prototype
description: Build a throwaway prototype to answer one design question. Use when a state model, piece of logic, or UI cannot be settled on paper and the user needs something concrete to react to.
---

# Prototype

A prototype is **throwaway code that answers a question**. The question decides the shape.

## Pick the branch

Identify which question is being answered, from the user's prompt, the surrounding code, or by asking:

- **"Does this logic / state model feel right?"** → build a single shareable HTML file: free-play controls plus a guided walkthrough that pushes the state machine through the cases that are hard to reason about on paper, drivable by a non-developer.
- **"What should this look like?"** → generate several **radically different** variations on one route, switchable by a URL search param and a floating switcher bar. Reach for `ascension:design-research` and `ascension:ux-patterns` first so the variations start from real layouts rather than assumptions.

The two branches produce very different artifacts, so getting this wrong wastes the whole prototype. If the question is genuinely ambiguous and the user is unreachable, pick by what the surrounding code is (a backend module → logic; a page or component → UI) and state the assumption at the top of the prototype.

## Rules for both

1. **Throwaway from day one, and marked as such.** Put the code next to what it is prototyping for, so the context is obvious, but name it so a casual reader sees it is not production. Obey the project's existing routing and file conventions; do not invent a new top-level structure.
2. **Trivial to run.** One command in the project's task runner, or a single HTML file the user double-clicks. No thinking required to start it.
3. **No persistence by default.** State lives in memory. Persistence is usually the thing being *checked*, not something to depend on. If the question genuinely involves a database, use a scratch one with a "PROTOTYPE, wipe me" name.
4. **Skip the polish.** No tests, no abstractions, no error handling beyond what makes it runnable. The point is to learn something fast — `ascension:test-first` does not apply to a prototype, and saying so out loud is how you avoid arguing with it later.
5. **Surface the state.** After every action (logic) or on every variant switch (UI), render the full relevant state so the user can see exactly what changed.
6. **Capture it when done.** Fold the validated decision into the real code, then keep the prototype as a **primary source**: commit it to a `prototype/<name>` branch off main and leave a pointer to that branch on the spec, ticket, or design doc. Record the verdict and the question it settled. Main keeps only the decision.

Throwaway is a constraint on *how the code is written*, not a promise to destroy it.

---
*Adapted from [mattpocock/skills](https://github.com/mattpocock/skills) (MIT).*
