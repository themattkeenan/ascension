# Code Reviewer Briefs

Two subagent prompts, run in parallel. Fill the placeholders and dispatch each with the Agent tool. Neither agent sees the other's material.

Shared placeholders:

- `{FIXED_POINT}` — the commit, branch, or tag the diff is measured from
- `{COMMIT_LIST}` — output of `git log {FIXED_POINT}..HEAD --oneline`
- `{DESCRIPTION}` — one paragraph on what was built

Both agents run:

```bash
git diff --stat {FIXED_POINT}...HEAD
git diff {FIXED_POINT}...HEAD
```

---

## Brief A — Standards

> You are reviewing a diff for conformance to this repository's coding standards.
>
> **What was built:** {DESCRIPTION}
> **Commits:** {COMMIT_LIST}
> **Diff:** `git diff {FIXED_POINT}...HEAD`
>
> **Documented standards for this repo:** {STANDARDS_FILES}
> (Read each one. A documented standard always overrides the smell baseline below.)
>
> **Smell baseline** — apply this even where the repo documents nothing. Each entry reads *what it is* → *how to fix*. Every one is a labelled judgement call, never a hard violation.
>
> - **Mysterious Name** — a function, variable, or type whose name does not reveal what it does or holds. → rename it; if no honest name comes, the design is murky.
> - **Duplicated Code** — the same logic shape in more than one hunk or file in this change. → extract the shape, call it from both.
> - **Feature Envy** — a method reaching into another object's data more than its own. → move the method onto the data it envies.
> - **Data Clumps** — the same few fields or params keep travelling together. → bundle them into one type.
> - **Primitive Obsession** — a primitive or string standing in for a domain concept. → give the concept its own small type.
> - **Repeated Switches** — the same switch or if-cascade on the same type recurs across the change. → polymorphism, or one map both sites share.
> - **Shotgun Surgery** — one logical change forces scattered edits across many files. → gather what changes together into one module.
> - **Divergent Change** — one file edited for several unrelated reasons. → split so each module changes for one reason.
> - **Speculative Generality** — abstraction, parameters, or hooks for needs the spec does not have. → delete it; inline back until a real need appears.
> - **Message Chains** — long `a.b().c().d()` navigation the caller should not depend on. → hide the walk behind one method.
> - **Middle Man** — a class or function that mostly just delegates onward. → cut it, call the real target.
> - **Refused Bequest** — a subclass that ignores or overrides most of what it inherits. → composition instead.
>
> Also check: error handling on the paths this change touches, boundary and edge cases, whether the tests assert on real behaviour rather than mock interactions, and any security surface the diff opens (`ascension:security-protocol` names the trust boundaries).
>
> **Report**, per file and hunk where relevant:
> (a) every place the diff breaks a **documented** standard — cite the file and rule;
> (b) every **baseline smell** you spot — name it and quote the hunk.
>
> Distinguish hard violations from judgement calls. Skip anything tooling already enforces. Use the format below. Under 500 words.

---

## Brief B — Spec

> You are reviewing a diff for faithfulness to the specification it was built from.
>
> **What was built:** {DESCRIPTION}
> **Commits:** {COMMIT_LIST}
> **Diff:** `git diff {FIXED_POINT}...HEAD`
>
> **The spec:** {SPEC_PATH_OR_CONTENTS}
> (Read it in full before reading the diff.)
>
> **Report:**
> (a) requirements the spec asked for that are **missing or partial**;
> (b) behaviour in the diff that was **not asked for** — scope creep;
> (c) requirements that look implemented but where the implementation appears **wrong**.
>
> Quote the spec line for every finding. Where the spec is silent on something the diff decides, say so — an undocumented decision is a finding, not a pass. Use the format below. Under 500 words.

---

## Report format (both agents)

```
### Strengths
[What is genuinely well done. Be specific — file:line.]

### Findings

#### Critical
[Defects, security holes, data-loss vectors, broken behaviour, or a
 requirement that is simply not implemented]

#### Important
[Structural problems, weak error handling, test gaps, scope creep]

#### Minor
[Style, optimisation, documentation polish]

### Verdict
Ready to land: [Yes / No / After fixes]
Rationale: [1-2 sentences]
```

Every finding carries: **file:line**, what is wrong, why it matters, and how to fix it where that is not obvious.

Classify by genuine severity — not everything is Critical, and inflating a nitpick costs the reader more than missing it. Do not rubber-stamp, do not comment on code outside the diff, and do not dodge the verdict.
