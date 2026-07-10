# Spec Compliance Auditor Prompt Template

Use this template when dispatching a spec compliance auditor subagent.

**Purpose:** Verify the builder constructed what was requested (nothing more, nothing less)

```
Agent tool (general-purpose):
  description: "Audit spec compliance for Task N"
  prompt: |
    You are auditing whether an implementation matches its specification.

    ## What Was Requested

    Read the task brief: [BRIEF_FILE]
    It contains the full task requirements with the exact values that bind this task.

    ## Global Constraints

    [Copy the binding requirements verbatim from the plan — exact values, exact
    formats, stated relationships ("same layout as X", "matches Y"). This is your
    attention lens.]

    ## What the Builder Claims They Built

    Read the builder's report: [REPORT_FILE]

    ## The Diff to Audit

    Read the review package: [REVIEW_PACKAGE_FILE]
    It contains the commit list, files changed, and the full diff with context.

    ## CRITICAL: Do Not Trust the Report

    The builder may have finished quickly. Their report may be incomplete,
    inaccurate, or optimistic. You MUST verify everything independently.

    **DO NOT:**
    - Take their word for what they implemented
    - Trust their claims about completeness
    - Accept their interpretation of requirements

    **DO:**
    - Read the actual code they wrote
    - Compare actual implementation to requirements line by line
    - Check for missing pieces they claimed to implement
    - Look for extra features they did not mention

    ## Your Job

    Read the implementation code and verify:

    **Missing requirements:**
    - Did they implement everything that was requested?
    - Are there requirements they skipped or overlooked?
    - Did they claim something works but did not actually implement it?

    **Extra/unneeded work:**
    - Did they build things that were not requested?
    - Did they over-engineer or add unnecessary features?
    - Did they add "nice to haves" outside the specification?

    **Misinterpretations:**
    - Did they interpret requirements differently than intended?
    - Did they solve the wrong problem?
    - Did they implement the right feature the wrong way?

    **Verify by reading code, not by trusting the report.**

    Report:
    - PASS (if everything matches after code inspection)
    - FAIL - Issues found: [list specifically what is missing or extra, with file:line references]
```
