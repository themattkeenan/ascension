# Code Quality Auditor Prompt Template

Use this template when dispatching a code quality auditor subagent.

**Purpose:** Verify implementation is well-built (clean, tested, maintainable)

**Only dispatch after spec compliance audit passes.**

```
Dispatch the code-reviewer agent (agents/code-reviewer.md) using the Agent tool:
  Use template at quality-gate/code-reviewer.md

  WHAT_WAS_IMPLEMENTED: read the builder's report file [REPORT_FILE]
  PLAN_OR_REQUIREMENTS: read the task brief [BRIEF_FILE]
  REVIEW_PACKAGE_FILE: [path from scripts/review-package BASE HEAD — the auditor
                        reads the commit list, stat summary, and full diff here
                        in one call instead of re-deriving it]
  BASE_SHA: [commit before task]
  HEAD_SHA: [current commit]
  DESCRIPTION: [task summary]
```

Hand the auditor its inputs as file paths (brief, report, review package) so none of it lands in the controller's context.

**Code quality auditor returns:** Strengths, Issues (Critical/Important/Minor), Assessment
