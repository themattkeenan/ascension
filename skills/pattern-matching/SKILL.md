---
name: pattern-matching
description: Use when contributing code to an existing project - guarantees that every new line mirrors the established conventions, naming schemes, architectural layering, directory layout, and stylistic choices already present in the codebase rather than drifting toward generic AI defaults
---

# Pattern Matching

## Overview

Every codebase has a fingerprint. Code you add passes as native when no reviewer can tell where the original ends and your contribution begins. Every function, file, and folder you produce has a living precedent somewhere in the repository; if you cannot point to the model you followed, you have already introduced drift.

When the repository favors `snake_case`, you write `snake_case`. When services live under `src/domain/`, your service lands there too. When errors propagate through a custom `Result<T>` type, you adopt it. The codebase's convention outranks personal preference.

## When to Use

- Touching any file in a pre-existing repository
- Introducing new modules, routes, or components into an established architecture
- Performing refactors that reshape existing logic

**Unnecessary when:**
- Bootstrapping a greenfield project (see ascension:project-bootstrap)
- Writing disposable scripts that will never enter version control

## The Entry Protocol

Before producing any code in an existing repository:

1. **Survey:** Locate 2-3 files or functions that solve a comparable problem.
2. **Catalog:** Record every convention they share:
   - Identifier formatting (camelCase, snake_case, PascalCase)
   - Directory taxonomy (where do peers of this file reside?)
   - Import conventions (relative paths, aliases, barrel files)
   - Error propagation strategy (exceptions, Result types, error codes)
   - State access patterns (global store, context injection, parameter passing)
   - Test co-location and framework choices
3. **Replicate:** Produce your code using the identical conventions.
4. **Audit:** Hold your output beside the originals.

Done when every new file, function, and import in your output traces to a precedent you surveyed, and the output reads as though its neighbors' author wrote it.

## Dimensions of Conformity

### File-Level Conventions

```
BEFORE placing a new file:

1. Where do its siblings reside? (directory hierarchy)
2. How are filenames formed? (kebab-case.ts, PascalCase.tsx, snake_case.py)
3. What internal structure does each file follow? (imports, types, constants, logic, exports)
4. How are exports surfaced? (default export, named exports, re-export indexes)
```

### Function-Level Conventions

```
BEFORE writing a new function, class, or component:

1. Identify an existing function that addresses a similar concern
2. Mirror its shape:
   - Argument style (destructured object vs positional args)
   - Return envelope (Promise<T>, Result<T, E>, nullable)
   - Error strategy (try/catch, .catch(), explicit error returns)
   - Observability (logger.info(), structured JSON, console methods)
   - Validation technique (Zod, Joi, manual guards, type narrowing)
3. Rely on the SAME libraries and utilities the project already depends on
   - Native array methods stay native when the project uses them; ramda stays out
   - The project's fetch wrapper is the HTTP client; got stays out
   - The ORM already wired in is the ORM
```

### Architecture-Level Conventions

```
BEFORE inserting a new module or layer:

1. How is the codebase stratified?
   - Routes -> Controllers -> Services -> Repositories?
   - Pages -> Components -> Hooks -> Helpers?
   - Handlers -> Domain -> Infrastructure?
2. Which way do imports flow?
3. Where does business logic concentrate?
4. How do cross-cutting concerns surface? (auth middleware, logging wrappers, error boundaries)
```

## The Shadowing Method

The fastest route to conformity: locate the nearest relative and shadow it stroke for stroke.

```
1. LOCATE: "Find the file most similar to what I need to create"
2. STUDY: Absorb its structure, imports, error handling, naming, export style
3. DUPLICATE: Use it as a skeleton
4. SPECIALIZE: Swap in only the logic unique to your feature
5. COMPARE: Place them side by side — can you spot the newcomer?
```

**Effective prompting pattern:**
```
"Here is our existing payment service (src/services/payment.ts).
Produce the new subscription service using identical structure, patterns, and naming."
```

Showing a concrete example always outperforms describing rules in prose.

## Typical Divergence Patterns

| AI Tendency | Project Convention | Correction |
|---|---|---|
| `console.log()` | Structured logger via `winston` | Adopt the project's logging pipeline |
| Generic `try/catch` | Domain-specific `ServiceError` class | Throw and catch using the project's error hierarchy |
| Inline Tailwind classes | CSS Modules / styled-components | Follow the project's styling methodology |
| `axios` for HTTP | Custom `fetch` wrapper in `lib/http` | Use the wrapper the team built |
| New helper functions | Existing utility belt in `utils/` | Audit `utils/` before creating anything new |
| Flat file layout | Feature-folder nesting | Respect the existing directory blueprint |
| Default exports | Named exports throughout | Align with the repository's export convention |
| Loose `any` types | Strict TypeScript with generics | Match the project's type discipline |
| `let` declarations | `const` by default | Mirror the existing variable declaration habit |
| Raw SQL strings | ORM query builder | Use the project's data access layer |

## Pre-Commit Verification

Before finalizing, confirm:

- [ ] File resides in the appropriate directory (same neighborhood as its peers)
- [ ] Filename obeys the prevailing naming scheme
- [ ] Import order and alias usage match the established style
- [ ] All identifiers (variables, functions, types) follow existing naming rules
- [ ] Error handling echoes the repository's standard pattern
- [ ] No new dependency duplicates an existing one
- [ ] Tests adhere to the same framework, structure, and naming as existing tests
- [ ] Logging channels through the project's logger
- [ ] The code reads as though the original author wrote it

## Guardrails

- Examine 2-3 analogous files before writing anything.
- Exhaust the project's existing utilities before inventing new ones.
- Reproduce error handling, logging, and validation patterns verbatim.
- Use the library already wired in for a job, even when a newcomer is better in isolation; a second library for the same job is duplication, not an upgrade.
- When the established convention looks flawed, raise it with the user before departing from it: "The codebase uses X. Shall I continue with X or migrate to Y?" Changing a convention is the user's call, made before the first divergent file, since one sets the precedent for the next ten.

## Related

`ascension:codebase-research` discovers the project's patterns; this skill applies them. `ascension:ux-patterns` and `ascension:design-integration` are the same discipline applied to UI components and design systems.

## Matching the amount, not just the style

Conventions govern *how* code is written. `ascension:minimalism` governs *how much* — the ladder that checks whether the thing needs to exist, whether this codebase already has it, and whether the standard library or the platform covers it before any new code is written.

Rung 2 of that ladder is this skill's territory: the most common form of slop is re-implementing what already lives a few files over. Look before you write.
