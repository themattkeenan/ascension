---
name: vocabulary
description: The shared words Ascension's skills think with. Use when a term from another skill needs defining, or when writing a skill, plan, spec, or report that should speak the same language as the rest of the framework.
---

# Vocabulary

These are **leading words**: compact concepts the skills reuse deliberately, so a single token anchors a whole region of behaviour. Use these words exactly; do not swap in synonyms.

## How the model behaves

**Context** — everything in the window this turn: system prompt, files read, tool results, the conversation.

**Smart zone / dumb zone** — early in a session the agent is sharp; as context grows it drifts into a dumb zone, forgetting instructions and repeating corrected mistakes. Same model, just more context. The onset is gradual and unannounced. See `ascension:context-economy`.

**Attention degradation** — the mechanism behind the dumb zone: each token's attention budget spreads across more competitors, so the signal on any one relationship shrinks. Recover by **removing** context, not adding more.

**Parametric knowledge** — what the model knows from training, bounded by a **knowledge cutoff**. Confident, unsourced, and possibly stale.

**Hallucination** — a confident claim with nothing behind it. The cure is a source, not a firmer instruction.

## Sources and pointers

**Primary source** — the thing itself: the code, the transcript, the raw log, the actual API response. Complete, authoritative, expensive to load.

**Secondary source** — an account of the thing: the summary, the doc, the compaction. Cheap, lossy, and on its own schedule since the moment it was written. Always leave a **pointer** from a secondary source back to its primary so the lossiness stays recoverable.

**Context pointer** — a reference held in context that names out-of-context material and encodes the condition for reaching it. A skill's `description` is one; a line in `CLAUDE.md` naming a doc is the same object. The pointer's *wording*, not its target, decides whether the material gets reached.

**Progressive disclosure** — pushing reference material behind a pointer so it costs nothing until it is needed.

## Doing the work

**Tight loop** — a feedback signal that is fast, deterministic, and **red-capable**: it goes red on *this* bug and green once fixed. The central artifact of `ascension:fault-diagnosis`.

**Seam** — the place where a module's interface lives; where you can alter behaviour without editing in that place. Tests live at seams. See `ascension:module-design`.

**Deep module** — a lot of behaviour behind a small interface. Produces **leverage** for callers and **locality** for maintainers.

**Tracer bullet** — a narrow but complete vertical slice through every layer, verifiable on its own. The unit of `ascension:work-tickets`.

**Vertical slice** vs **horizontal slice** — through all layers for one behaviour, versus across one layer for all behaviours. Prefer vertical.

**Frontier** — everything whose prerequisites are settled and which can therefore be taken *now*. Used for questions in `ascension:grilling` and for tickets in `ascension:work-tickets`.

**Phase boundary** — the gap between two chunks of work; the only place a context decision belongs. See `ascension:context-economy`.

**Prefactor** — restructuring before the change, so the change becomes easy. "Make the change easy, then make the easy change."

**The ladder** — the ordered check before writing code: does it need to exist, is it already here, does the standard library or the platform do it, can it be one line. Climbing it is being **lazy**, which means efficient, never careless. See `ascension:minimalism`.

**Over-build** — code the task did not need: an abstraction with one implementation, a dependency for what a few lines do, config nobody sets. `ascension:overbuild-review` names the five kinds.

**Think in code** — computing an answer with a script and printing only the result, rather than reading the raw data into the window. See `ascension:think-in-code`.

**Expand–contract** — the safe sequence for a change too wide to slice: add the new form beside the old, migrate call sites in batches, delete the old form last.

## Writing for agents

**Context load** — the cost of always-loaded material on the window: every skill description, every `CLAUDE.md` line, spent every turn whether or not it fires.

**Cognitive load** — the cost on the human: which documents exist and when to reach for each. Not a cost to minimise — it is the price of human agency.

**Completion criterion** — the condition that tells the agent a step is done. Vague bounds invite **premature completion**.

**No-op** — an instruction the model already obeys by default. It pays load to say nothing. See `ascension:protocol-authoring`.

---
*Distilled from [mattpocock/dictionary-of-ai-coding](https://github.com/mattpocock/dictionary-of-ai-coding) and [mattpocock/skills](https://github.com/mattpocock/skills) (MIT).*
