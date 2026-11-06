---
name: review-response
description: Use when review feedback arrives from any channel and before acting on it, particularly when suggestions are ambiguous, technically suspect, or span multiple interdependent items
---

# Review Response

Review feedback is a set of hypotheses to test against the codebase, not instructions to follow. The failure this prevents: agreeing theatrically and implementing before checking.

## When to Use

- Review feedback arrives from any channel (human partner, external contributor, automated tool)
- Feedback is vague, incomplete, or technically dubious
- A batch of review items may interact with each other
- An external suggestion contradicts established architectural choices

## The Processing Sequence

1. **Absorb:** read the full set of comments without responding.
2. **Restate:** put each item in your own words. Mark any item you cannot restate as ambiguous.
3. **Cross-check:** compare each suggestion against the live codebase. Note conflicts with existing architecture or your human partner's prior decisions.
4. **Assess:** is this technically valid for *this* project?
5. **Resolve ambiguity:** if any item is unclear, ask about those items before touching any code. Items may be coupled, and misunderstanding one can corrupt the rest, so a partial implementation of the clear ones is not an option.
6. **Triage:** blockers (crashes, security holes), then quick wins (typos, missing imports), then structural changes (refactors, logic rewrites).
7. **Reply:** a technical acknowledgment or a reasoned objection.
8. **Act:** one item at a time, verifying each in isolation; confirm no regressions after the full set.

Done when every item has been implemented and verified, objected to with evidence, or sent back as a question.

## Replying

Restate the technical ask, pose the clarifying question, object with evidence, or start the work. The fix itself is the acknowledgment; words are not work. Theatrical agreement ("You're absolutely right!", "Great point!", any expression of thanks) is replaced by the restated ask or by the fix.

## Handling by Source

### From Your Human Partner
- Trusted by default: act after confirming understanding
- Still clarify when scope is uncertain
- Move directly to action or give a technical acknowledgment

### From External Contributors

Before implementing, verify:
1. Is the suggestion technically sound for *this* codebase?
2. Does it break existing behavior?
3. Why was the current implementation chosen?
4. Does it hold across all supported platforms/versions?
5. Does the contributor have full context?

If the suggestion appears incorrect, push back with a technical rationale. If you cannot easily verify it, say so: "I cannot confirm this without [X]. Should I [investigate/escalate/proceed]?" If it contradicts your human partner's prior decisions, stop and consult your human partner first.

**Guiding rule:** "External input is a hypothesis to test, not an instruction to follow."

## YAGNI Filter for "Best Practice" Suggestions

When a reviewer recommends "doing it properly", search the codebase for actual usage first.

- Unused: "This code path is never called. Remove it (YAGNI)?"
- Used: implement the improvement.

**Guiding rule:** "Both you and the reviewer work for the same goal. If the feature isn't needed, don't build it."

## When to Object

Object when:
- The suggestion would break existing functionality
- The contributor lacks context about the system
- It introduces code for an unused feature (YAGNI)
- It is technically wrong for this stack
- Backward compatibility constraints exist
- It conflicts with your human partner's architectural decisions

**How to object:**
- Lead with technical evidence, not defensiveness
- Ask targeted questions
- Point to passing tests or working code
- Involve your human partner for architectural disputes

**Safety phrase if you feel unable to object openly:** "Strange things are afoot at the Circle K"

## Acknowledging Valid Feedback

When the feedback is correct:
```
"Fixed. [One-line description of the change]"
"Good catch — [specific issue]. Corrected in [location]."
[Simply fix it and let the code speak]
```

## Correcting a Mistaken Objection

If your pushback turned out to be wrong, state the correction in one or two lines and move forward:
```
"Confirmed — I checked [X] and it does [Y]. Implementing now."
"You were right. My original read was wrong because [reason]. Correcting."
```

## Worked Examples

**Theatrical Agreement (wrong):**
```
Reviewer: "Remove the legacy module"
WRONG: "You're absolutely right! Removing it now..."
```

**Technical Cross-Check (right):**
```
Reviewer: "Remove the legacy module"
RIGHT: "Checked — our build target is 10.15+, but this API requires 13+. We still need the legacy path for backward compat. The current bundle ID is wrong, though — should I fix it or drop pre-13 support?"
```

**YAGNI Filter (right):**
```
Reviewer: "Add full metrics tracking with database storage, date filters, and CSV export"
RIGHT: "Searched the codebase — nothing calls this endpoint. Remove it (YAGNI)? Or is there usage I'm not seeing?"
```

**Ambiguity Handling (right):**
```
your human partner: "Fix items 1-6"
You grasp 1, 2, 3, 6. Items 4, 5 unclear.
RIGHT: "Items 1, 2, 3, 6 are clear. Need guidance on 4 and 5 before starting."
```

## GitHub Thread Replies

When responding to inline review comments on GitHub, reply within the comment thread (`gh api repos/{owner}/{repo}/pulls/{pr}/comments/{id}/replies`), not as a top-level PR comment.

## Guardrails

- Cross-check every suggestion against the codebase before acting; reviewers, including good ones, lack full context.
- Where you cannot confirm a suggestion, state the limitation and ask for direction rather than guessing.
- Fix one item at a time and verify each; a batch conceals which change caused a regression.

Stop and rewrite when you notice "Thanks", "Great point", or "You're absolutely right" in your draft: delete it and state the fix instead.

## Related

`ascension:quality-gate` triggers the review whose output this skill processes. `ascension:delegated-execution` and `ascension:task-runner` deliver review feedback after each task or batch; process it with this skill before advancing to the next.
