---
name: protocol-authoring
description: Use when creating new skills, editing existing skills, writing CLAUDE.md or AGENTS.md, or validating that a skill actually changes behavior before deploying it
---

# Protocol Authoring

A reference for writing any document an agent consumes: a skill, a `CLAUDE.md`, a doc reached by a pointer. The packaging differs; the writing does not. The same levers make each one predictable, because the agent takes the same *process* every run rather than producing the same output.

A skill is a **process document**, not a summary of one. If reading the description told the agent what to do, the body will never be read.

## Context pointers

A **context pointer** is a reference held in context that names out-of-context material and encodes the condition for reaching it. A skill's `description` is one; a line in `CLAUDE.md` naming a doc is the same object.

The pointer's **wording**, not its target, decides when the material gets reached and how reliably. Excellent material behind a weak pointer is a variance bug: sharpen the wording first, and inline the material only if sharpening fails.

A pointer does two jobs: say what the material is, and list the **branches** that should trigger reaching it. Every word of an always-loaded pointer costs on every turn, so it earns harder pruning than the body:

- **Front-load the leading word.** The pointer is where it does its triggering work.
- **One trigger per branch.** Synonyms renaming a single branch are one branch written twice. Collapse them; keep only genuinely distinct branches.
- **Cut identity the body already carries.**

### Writing the description

```yaml
# too abstract, no trigger
description: Best practices for testing

# a workflow summary — the agent may follow this instead of reading the skill
description: Write test, run it, watch it fail, then implement

# first person
description: I help you write better tests

# good: trigger conditions, no workflow
description: Use when implementing any feature or bugfix, before writing implementation code

# good: distinct branches, one trigger each
description: Use when a merge, rebase, or cherry-pick has stopped with conflicts
```

Write it in the third person, lead with the condition, and cover the words a user would actually say. Keep the workflow out of it.

## The two loads

Every document and pointer you add spends one of two budgets:

- **Context load** — the cost of always-loaded material on the window: a skill description, a `CLAUDE.md` line, anything sitting in context every turn, spending tokens and attention whether or not it fires.
- **Cognitive load** — the cost on the human: which documents exist and when to reach for each. Not a cost to minimise. It is the price of human agency; spend it where human judgement matters, remove it where it does not.

Material reached only through a pointer escapes context load at the price of the pointer's own line.

Adding a skill to Ascension adds its description to every session forever. That is the price; make the skill worth it or fold it into an existing one.

## Information hierarchy

A document is built from two content types: **steps** (ordered actions) and **reference** (definitions, rules, facts consulted on demand). They mix freely. The decision is where each piece sits on a ladder ranked by how immediately the agent needs it:

1. **In-file step** — the primary tier: what the agent does, in order.
2. **In-file reference** — consulted on demand. Often a legitimately flat peer-set (every rule of a review on one rung). That is fine, not a smell.
3. **Disclosed reference** — pushed into a separate file behind a pointer, loaded only when the pointer fires.

Push too little down and the top bloats; push too much and you hide what the agent needs. That tension is the whole decision.

**Progressive disclosure** is the move down the ladder so the top stays legible. Branching is the cleanest test: inline what every branch needs, push behind a pointer what only some branches reach. In a document that has steps, undisclosed reference buries them and turns attending to them into a coin flip.

**Co-location** decides what sits *beside* a piece once its rung is chosen: keep a concept's definition, rules, and caveats under one heading rather than scattered, so reading one part brings its neighbours.

**Sprawl** is the failure here — a document simply too long, even when every line is live and unique. Attention thins across the excess. The cure is the ladder: disclose reference behind pointers, split by branch or sequence so each path carries only what it needs.

## Steps and completion criteria

Every step ends on a **completion criterion**: the condition telling the agent the work is done. Two properties make it a lever.

**Clarity** — can the agent tell done from not-done? A vague bound ("understanding reached") invites **premature completion**, attention slipping to *being done* while later steps still visible in the document supply the pull. Defend in order: sharpen the bound first (local and cheap); only if it is irreducibly fuzzy *and* you observe the rush, split the sequence so the later steps are out of view. Hiding works only across a real context boundary — a handoff or a subagent dispatch. An inline call leaves the later steps in context and clears nothing.

**Demand** — how much the criterion requires. *"Every modified model accounted for"* forces thorough work where *"produce a change list"* does not. Demand is not step-bound: *"every rule applied"* binds a body of flat reference just as *"every step done"* binds a sequence.

The strongest criteria are both checkable and exhaustive. Prefer a command with observable output over a self-report — see `ascension:completion-gate`.

## Leading words

A **leading word** is a compact concept already living in the model's pretraining that the agent thinks with while running the document: *seam*, *tracer bullet*, *fog of war*, *tight* loop. Repeated as a token, never as a sentence, it accumulates a distributed definition and anchors a whole region of behaviour in very few tokens, by recruiting priors the model already holds.

Coining your own works if you define it clearly, but a made-up word recruits no priors — you pay in definition tokens what a pretrained word gives free. Reach for an existing word first.

It anchors twice. In the body, *execution*: the agent reaches for the same behaviour every time the word appears. In a pointer, *invocation*: when the same word lives in your prompts, your docs, and your codebase, the agent links that shared language to the material and reaches it more reliably.

Hunt for refactors into leading words. "Fast, deterministic, low-overhead" → *tight*. "A test suite you believe in" → *red*, turning a fuzzy gate into a binary observable state. Assume every document is carrying restatements that leading words retire.

Ascension's shared set lives in `ascension:vocabulary`. Use those words rather than synonyms.

## Negation

Steering by prohibition drags the forbidden behaviour into context and makes it **more** available, not less. *Don't think of an elephant*, and the elephant is all there is; the negation is a weak modifier that the strongly-activated concept overruns, so the ban half-reads as an instruction.

Prompt the **positive**: state the target behaviour ("write one-line comments") so the banned one is never spoken. A prohibition earns its place only as a hard guardrail you cannot phrase positively — and even then, pair it with the positive target so attention lands on what to do.

This is why a long table of rationalizations is usually a liability: it is a list of excuses, written down, in context, phrased in the agent's own voice. One or two entries for the failure you have actually observed is a guardrail. Twelve is a script. When persuasion framing is genuinely needed for a hard guardrail, [persuasion-principles.md](persuasion-principles.md) covers the mechanics.

## Pruning

- **Single source of truth.** Keep each meaning in one authoritative place, so changing the behaviour is a one-place edit. **Duplication** costs maintenance and tokens, and inflates a meaning's prominence past its real rank. (The accidental inverse of a leading word, which repeats a token on purpose, never the meaning.)
- **The environment is a source of truth too** — `package.json` scripts, config files, the directory layout, `--help` output. A document restating it is a **cache**: a copy of a lookup, earning its load only when the lookup is expensive. Cache what the agent cannot find by looking — the unwritten convention, the reason behind a choice, the gotcha no config confesses. Leave the one-command lookups to the environment, where they cannot go stale.
- **Relevance.** Does each line still bear on what the document does? Lines lose relevance by never bearing on the task (exposition, or a branch that should be disclosed) or by going stale. Without a pruning discipline the default fate is **sediment**: stale layers that settle because adding feels safe and removing feels risky.
- **No-ops.** Hunt them sentence by sentence: an instruction the model already obeys by default pays load to say nothing. The test — does it change behaviour versus the default? — is model-relative, and two people disagreeing about a no-op are disagreeing about the default; settle it by running the document, not by debate. When a sentence fails, delete the whole sentence rather than trimming words from it.
- **Invented evidence.** Never state a metric you did not measure. "First-attempt fix rate: 95% vs 40%" reads as authority and is fabricated; it also teaches the agent that unsourced numbers are acceptable output.

## Ascension mechanics

### Layout

```
skills/<skill-name>/
├── SKILL.md              # required: frontmatter + body
├── <REFERENCE>.md        # disclosed reference, reached by pointer
└── scripts/              # executables the skill runs
```

Names are `lowercase-with-hyphens`, gerund or noun phrases that describe the work (`fault-diagnosis`, `test-first`, `merge-conflicts`).

Frontmatter carries `name` and `description` only. Cross-reference other skills as `ascension:<name>`, and when a step should actually *run* another skill, say so operatively: *call the Skill tool with "ascension:grilling"*. One skill per call; a step needing two is two calls.

### Flowcharts

Use a `dot` graph where control flow genuinely branches — a decision tree, a loop with exits. Keep the labels short and put the substance in prose beneath it. A flowchart that restates a linear list of steps is duplication with extra syntax.

### Code examples

One language unless the skill is genuinely cross-language; a second language usually dilutes rather than clarifies. Show the shortest version that carries the point, and label good and bad explicitly so the contrast is not left to inference.

## Validate before deploying

A skill that has never been run is a hypothesis. Establish the baseline first, then check the skill moved it:

1. **RED** — give a fresh subagent the task **without** the skill. Record what it actually does. This is the default behaviour you are trying to change; without it you cannot tell whether the skill did anything.
2. **GREEN** — give a fresh subagent the same task **with** the skill. Compare.
3. **REFACTOR** — where behaviour did not move, the fix is usually a sharper completion criterion, a stronger leading word, or a positive restatement of something currently phrased as a ban. Re-run.

[testing-skills-with-subagents.md](testing-skills-with-subagents.md) has the full method, including how to construct pressure scenarios and read the results.

## Before you ship it

- [ ] The description leads with the trigger condition, covers each distinct branch once, and summarises no workflow.
- [ ] Steps are in order, and each ends on a criterion the agent can check.
- [ ] Reference that only some branches need sits behind a pointer.
- [ ] Every meaning appears once.
- [ ] Guardrails are phrased as the positive target wherever they can be.
- [ ] No fabricated metrics, no no-op sentences.
- [ ] The RED baseline was run, and the GREEN run behaved differently.

---
*The context-pointer, two-loads, information-hierarchy, completion-criteria, leading-word, and pruning material is adapted from [mattpocock/skills](https://github.com/mattpocock/skills) (MIT).*
