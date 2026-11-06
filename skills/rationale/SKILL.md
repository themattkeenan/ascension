---
name: rationale
description: Use when the user requests new work - a feature, component, integration, dependency, or addition - before designing or building it, to settle whether the work is worth doing. Also use when the request arrived as a solution rather than a problem, or when the scope looks larger than the user seems to think.
---

# Rationale

The first question is not "how do we build this." It is "should we."

Most wasted engineering effort traces back to building the wrong thing, not building the thing wrong. The cheapest feature is the one that never gets built. This skill interrogates the request before any design or code.

## Proportionality comes first

Scale the analysis to the request. A one-line ask gets a one-line pass. A new subsystem gets the full five points. Spending twenty minutes justifying a twenty-minute change is the failure this skill has to avoid, not the discipline it exists to enforce.

**Run the full analysis for:**

- A new feature, component, module, integration, or dependency
- Scope that looks larger than the user seems to think ("just add auth")
- A request that arrived as a solution ("add a caching layer") rather than a problem ("the page loads slowly")
- Requests driven by what another product has, or by something the user saw online
- Anything that adds a thing to keep alive: a service, a scheduled job, a schema, a config surface

**Skip it for:**

- Bugs. The problem is self-evident; call the Skill tool with "ascension:fault-diagnosis".
- Work the user has already reasoned through out loud, or that has already passed through this skill
- Direct commands: delete this file, run this script, rename that
- Security patches, typos, formatting, trivial config

## The five points

### 1. Reality check

- What specific problem does this solve?
- Who hits it, and how often?
- What happens if nothing is done?
- Is this an observed pain or a hypothetical one?
- Did the user describe a **problem** or jump straight to a **solution**?

That last one matters most. Trace every request back to the problem before evaluating the proposed solution. The stated solution is one of several answers to the problem, and often not the best one.

### 2. True cost

| Size | Time | Shape |
|---|---|---|
| Trivial | Minutes | One file, no new dependency, no architectural impact |
| Moderate | Hours | Several files, maybe a new dependency, contained |
| Substantial | Days | A new subsystem or pattern, test infrastructure to build |
| Massive | Weeks | Architectural change, new service, data migration, cross-cutting |
| Ongoing | As long as it runs | Anything with a schedule, a queue, an inbox of failures, or a user expecting a reply |

Count the parts the first estimate leaves out: tests, docs, review and iteration, and the maintenance the thing demands for as long as it exists. Then multiply the first estimate by 2.5.

**Watch the ongoing row.** It hides inside the others. A one-off import script is Moderate. A nightly sync with retries and alerts is a commitment, whatever it costs at the keyboard, and the difference is not effort but ownership.

### 3. What already exists

Exhaust the alternatives before recommending a build.

1. Does a library or package solve this? (npm, pip, crates, gems)
2. Does a service handle it? (Stripe, Auth0, Twilio, and their peers)
3. Is there an open-source project that does 80% of it?
4. Does something already in this codebase cover it, or extend to cover it?
5. Is there a 10% effort path that delivers 80% of the value?

For each candidate: how much of the need it covers, what it costs, and what it locks the user into.

Call the Skill tool with "ascension:reference-engine" to find existing implementations, and with "ascension:github-search" when an open-source answer is plausible. When the question is whether the thing needs to exist at all, call the Skill tool with "ascension:minimalism" and climb the ladder.

### 4. The six-month view

- What would a sceptical staff engineer challenge about this approach?
- What does it cost to keep alive after it ships: maintenance, cognitive complexity, testing surface, onboarding?
- What bites in six months: scaling, security surface, dependency rot, migration pain when requirements change?
- Is this treating a symptom or the cause?
- Does this add complexity the problem does not require?

### 5. Decision point

Present real options, not a rubber stamp with a caveat.

```
A) Build as requested
   Effort: [size]. Trade-offs: [honest]

B) Build a smaller version
   Covers: [the 80%]
   Skips: [the 20% that costs most of the effort]
   Effort: [size]

C) Use something that already exists
   What: [library, service, existing code]
   Coverage: [how much of the need]
   Trade-offs: [cost, lock-in, limits]

D) Do not build it, or not yet
   Why: [not needed, premature, wrong sequence, better timing later]
   Instead: [the smallest thing that tests the same assumption]
```

Always include A and D. They are the ends of the spectrum, and leaving either out turns the analysis into theatre.

Recommend **one** option and say why. "It depends on your priorities" is not a recommendation.

Then stop and wait for the pick. The options are the deliverable of this skill; the first line of code belongs to whichever option the user chooses, including when that is your recommendation and including when the request said "today". Urgency shortens the analysis. It does not move the build ahead of the decision.

## Output format

```
## Is this worth building: [request]

**The actual problem:** [restated in your own words]

**Size:** [classification], because [one line]

**Already exists:** [option, coverage, trade-off] x however many are real

**What it costs after it ships:** [the part nobody mentioned]

**Options**
A) ... B) ... C) ... D) ...

**Recommendation:** [one option, with the reason]
```

## Done

The analysis is done when the user has picked an option, nothing has been built yet, and the pick is written down with its reason and the options that lost: in the design doc or spec if one exists, otherwise in the plan or ticket for the work. When the choice is hard to reverse, record it as an ADR by calling the Skill tool with "ascension:domain-modeling". A decision that lives only in the conversation gets relitigated next week by a session that never saw the reasoning.

Once the user picks A, B, or C, call the Skill tool with "ascension:intent-discovery" to design it. On D, write down why and stop.

## Guardrails

- Restate the problem before evaluating the solution.
- Give the effort estimate you believe, including when it disappoints.
- Give one clear recommendation, and accept the decision once it is made.
- Stay proportional. The analysis is shorter than the work it is about.
- You are an advisor, not a gate. This skill improves decisions; it never stalls work.

## Edge cases

**The user pushes back.** State the reasoning once, clearly. If they still want A, build A.

**It is genuinely urgent.** Compress to problem, size, one alternative. Name the risk the urgency is hiding. Put the pick to the user in one line, and go with their answer.

**It is genuinely trivial.** Run the five points silently. If they resolve in under thirty seconds, say so in one line and get on with it.

---
*Descends from god mode's `rationale`. Rewritten in v2.2 to the `ascension:protocol-authoring` standard, sharing its shape with the Ascension Ops port.*
