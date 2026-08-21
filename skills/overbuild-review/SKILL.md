---
name: overbuild-review
description: Find over-engineering and report what to cut. Use to review a diff or a whole repo for reinvented standard library, needless dependencies, speculative abstractions, and dead flexibility, or to harvest deferred-shortcut markers into a debt ledger.
---

# Overbuild Review

`ascension:minimalism` pointed backwards. Three modes; pick by what the user asked for.

| Mode | Scope | Output |
|---|---|---|
| **diff** | The changes since a fixed point | Ranked cuts, `net: -N lines possible` |
| **repo** | The whole tree | Ranked cuts, biggest first, `net: -N lines, -M deps` |
| **debt** | Every `ascension:` shortcut marker | A ledger of deferrals and their triggers |

**Scope is over-engineering and complexity only.** Correctness bugs, security holes, and performance belong to `ascension:quality-gate` and `ascension:security-protocol`. Route them there; do not fold them in here, because a review that reports everything gets read as nothing.

This mode **lists findings and applies nothing** unless the user asks for the fixes.

## Tags

Every finding carries one:

- **`delete:`** dead code, unused flexibility, a speculative feature. Replacement: nothing.
- **`stdlib:`** a hand-rolled thing the standard library ships. Name the function.
- **`native:`** a dependency or block of code doing what the platform already does. Name the feature.
- **`yagni:`** an abstraction with one implementation, config nobody sets, a layer with one caller.
- **`shrink:`** the same logic in fewer lines. Show the shorter form.

## Format

One line per finding. Location, what to cut, what replaces it.

`<file>:L<line>: <tag> <what>. <replacement>.`

The difference between a useful finding and a useless one is entirely specificity:

❌ "This EmailValidator class might be more complex than necessary — have you considered whether all these validation rules are needed at this stage?"

✅ `L12-38: stdlib: 27-line validator class. "@" in the string, 1 line — real validation is the confirmation mail.`

✅ `L4: native: moment.js imported for one format call. Intl.DateTimeFormat, 0 deps.`

✅ `repo.py:L88: yagni: AbstractRepository with one implementation. Inline it until a second exists.`

✅ `L52-71: delete: retry wrapper around an idempotent local call. Nothing replaces it.`

✅ `L30-44: shrink: manual loop building a dict. dict(zip(keys, values)), 1 line.`

## Mode: diff

Take the fixed point the user names (a SHA, branch, tag, or `origin/main`) and read `git diff <fixed-point>...HEAD`. The diff's best outcome is getting shorter.

End with `net: -<N> lines possible.` Nothing to cut: `Lean already. Ship.`

## Mode: repo

Scan the tree instead of a diff, and rank biggest cut first. Where to hunt:

- Dependencies the standard library or the platform already ships
- Single-implementation interfaces and factories with one product
- Wrappers that only delegate onward
- Files exporting exactly one thing
- Dead feature flags and configuration nobody sets
- Hand-rolled versions of standard library functions

Weight recently-changed areas: deepening pays off where change is actually happening (`git log --oneline` finds the hot spots). `ascension:module-design`'s **deletion test** is the check for anything you suspect is a pass-through — delete it mentally, and see whether complexity vanishes or reappears across N callers.

End with `net: -<N> lines, -<M> deps possible.`

## Mode: debt

Every deliberate shortcut taken under `ascension:minimalism` is marked with an `ascension:` comment naming its ceiling and upgrade path. Harvest them:

```bash
grep -rnE '(#|//|--) ?ascension:' . --exclude-dir=node_modules --exclude-dir=.git
```

One row per marker, grouped by file:

`<file>:<line> — <what was simplified>. ceiling: <the limit named>. upgrade: <the trigger to revisit>.`

Tag any marker that names **no upgrade trigger** as `no-trigger` — those are the ones that silently rot into permanent.

End with `<N> markers, <M> with no trigger.` Nothing found: `No deferred shortcuts. Clean ledger.`

Offer to persist it to `docs/TECH-DEBT.md` rather than writing it unasked.

## What is not bloat

A single smoke test or one `assert`-based self-check is the minimum, not excess — never flag it for deletion. Neither is validation at a trust boundary, error handling that prevents data loss, a security measure, or an accessibility affordance. Those are the floor, and `ascension:minimalism` protects them for the same reason.

## Honesty about savings

Report only what you counted. The unbuilt version was never written, so there is no baseline to subtract from — never claim a per-repo "you saved N lines" figure. `net: -N lines possible` is a count of the cuts you actually listed, and the debt ledger is a count of real markers. Both are checkable; an estimate is not.

---
*The tags, the one-line format, the net-lines scoring, and the debt-ledger idea are adapted from [ponytail](https://github.com/DietrichGebert/ponytail) by DietrichGebert (MIT).*
