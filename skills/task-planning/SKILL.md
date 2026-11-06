---
name: task-planning
description: Use when you have a spec or requirements for a multi-step task, before touching code
---

# Task Planning

## Overview

Produce thorough implementation plans written for an engineer with zero familiarity with the codebase and unreliable design instincts. Spell out everything: which files to touch per task, exact code, testing procedures, relevant documentation, how to verify each step. Deliver the plan as granular, self-contained tasks. DRY. YAGNI. TDD. Frequent commits.

Assume the engineer is technically competent but knows almost nothing about the project's tooling or problem domain. Assume their test design skills are weak.

**Announce at start:** "I'm applying the task-planning skill to build the implementation plan."

**Context:** This should run in a dedicated worktree (created during the intent-discovery phase).

**Save plans to:** `docs/plans/YYYY-MM-DD-<feature-name>.md`

## When to Use

**Required:**
- Multi-step features (touching more than one file or behavior)
- Bug fixes spanning multiple components
- Refactoring across module boundaries
- Any task requiring coordinated test + code + commit across files
- When an intent-discovery session produces a spec or design

**Not required:**
- Single-line fixes with obvious scope (typo, config value)
- Adding a single test for existing code
- Documentation-only changes
- Renaming a variable in one file

The bound is the file count: if it touches more than one file, write the plan.

## The Entry Protocol

Before writing a plan, confirm ALL of these:

1. **Design is approved** - An intent-discovery session or human partner has confirmed the approach
2. **Scope is defined** - You can enumerate every component that will change
3. **Requirements are concrete** - You know what "done" looks like (acceptance criteria, not feelings)
4. **Tech stack is identified** - You know the languages, frameworks, and libraries involved
5. **Test strategy is defined** - You know how each piece will be verified
6. **Worktree is active** - You are working in an isolated branch, not main; if not, call the Skill tool with "ascension:workspace-isolation"

Done when all six are confirmed. An unclear item goes back to your human partner, or to the Skill tool with "ascension:intent-discovery", before any plan is written; a gap in the plan becomes a defect in the code.

## Guardrails

- Every step names exact file paths.
- Every step carries complete code, not a description of it ("add validation" is not a step).
- Every verification step gives the exact shell command and its expected output.
- Every task lists each file created, modified, or deleted, and ends with a commit step carrying the precise commit message.
- Reference relevant skills by name (e.g., `ascension:test-first`).
- DRY, YAGNI, TDD, frequent commits.
- A spec says what; the plan says how, step by step, with file paths and commands. Write the plan even when the spec is detailed.
- Implementation begins only after the plan is saved and reviewed.

## Task Granularity

**Each step is one action (2-5 minutes):**
- "Write the failing test" -- step
- "Run it to confirm it fails" -- step
- "Implement the minimal code to pass" -- step
- "Run the tests to confirm they pass" -- step
- "Commit" -- step

## Plan Document Header

**Every plan opens with this header:**

```markdown
# [Feature Name] Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use ascension:task-runner to implement this plan task-by-task.

**Goal:** [One sentence describing what this builds]

**Architecture:** [2-3 sentences about the approach]

**Tech Stack:** [Key technologies/libraries]

---
```

## Task Template

````markdown
### Task N: [Component Name]

**Files:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py:123-145`
- Test: `tests/exact/path/to/test.py`

**Step 1: Write the failing test**

```python
def test_specific_behavior():
    result = function(input)
    assert result == expected
```

**Step 2: Run test to verify failure**

Run: `pytest tests/path/test.py::test_name -v`
Expected: FAIL with "function not defined"

**Step 3: Write minimal implementation**

```python
def function(input):
    return expected
```

**Step 4: Run test to verify it passes**

Run: `pytest tests/path/test.py::test_name -v`
Expected: PASS

**Step 5: Commit**

```bash
git add tests/path/test.py src/path/file.py
git commit -m "feat: add specific feature"
```
````

The plan is complete when every task follows this template with no placeholder left unfilled, and it is saved under `docs/plans/`.

## Too big for one session?

A plan whose tasks will not each fit in a fresh context window is not a plan yet — it is a map. Call the Skill tool with "ascension:work-tickets" to slice it into tracer bullets with explicit blocking edges, each sized to one context window and verifiable on its own.

The tell: a task whose description needs the previous task's conversation to make sense. That is a ticket boundary in the wrong place.

## Execution Handoff

Plan → execution is a phase boundary. Call the Skill tool with "ascension:context-economy" and let its answer decide which option below you recommend.

After saving the plan, offer the user a choice:

**"Plan complete and saved to `docs/plans/<filename>.md`. Two execution options:**

**1. Delegated Execution (this session)** - I dispatch a fresh subagent per task, review between tasks, fast iteration

**2. Separate Session** - Open a new session with task-runner, batch execution with checkpoints

**Which approach?"**

**If Delegated Execution chosen:**
- Call the Skill tool with "ascension:delegated-execution"
- Stay in this session
- Fresh subagent per task + code review

**If Separate Session chosen:**
- Guide them to open a new session in the worktree
- The new session calls the Skill tool with "ascension:task-runner"

## Related

Fed by `ascension:intent-discovery`, which produces the spec or design this skill plans from. Executed by `ascension:task-runner` or `ascension:delegated-execution`; `ascension:merge-protocol` lands the result.
