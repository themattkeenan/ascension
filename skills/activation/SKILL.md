---
name: activation
description: Use when the user says "use ascension" or asks which skill fits a task - the always-on working rules and the map from situation to skill
---

# Ascension

Four working rules that apply to every task, and a map for the tasks big enough to need a skill. A question, a lookup, or a one-line edit needs neither a skill nor a ceremony: do it.

## Working rules

1. **Say what you are assuming.** When a wrong guess would be expensive to undo, ask once before starting; otherwise state the assumption and carry on.
2. **Build the least that solves the request.** Use what the codebase, the standard library, or an installed dependency already has. An abstraction earns its place on its second use.
3. **Every changed line traces to the request.** Match the code around it, and remove only what your own change left unused.
4. **Turn the task into a check you can run, then run it.** Done means you ran it and read the output; say what you ran and what it showed.

## The map

When the situation on the left is the one in front of you, call the Skill tool with the skill on the right (`ascension:<name>`) before starting that piece of work.

| Situation | Skill |
|---|---|
| A new feature or change whose design is not settled | `intent-discovery` (it runs `grilling`, `reference-engine`, `prototype` as needed) |
| The request arrived as a solution, or may not be worth building | `rationale` |
| The design is agreed and the work is more than a few steps | `task-planning`, then `work-tickets` if it will not fit one session |
| Writing code in an existing project | `codebase-research`, `pattern-matching`, `minimalism`, `test-first` |
| Building or changing a user interface | `ux-patterns`, `ui-engineering`, `design-research` for a whole site |
| Something is broken or slow | `fault-diagnosis` |
| A merge or rebase stopped on conflicts | `merge-conflicts` |
| Code that takes user input, handles auth, or holds secrets | `security-protocol` |
| A feature sized diff is ready | `quality-gate`, then `completion-gate`, then `merge-protocol` |
| About to say fixed, passing, or done | `completion-gate` |
| Handing work to subagents | `delegated-execution`, `agent-messaging`, `task-runner`, `workspace-isolation` |
| A phase just ended, the session is long, or work is moving elsewhere | `context-economy` |
| Behaviour needs pinning down before code | `specification-first` |
| A project term is fuzzy, or a decision is hard to reverse | `domain-modeling` |
| Writing a skill or a `CLAUDE.md` | `protocol-authoring` |

## "Use ascension"

Say where the task lands on the map and which way you will run it, with one line of reason, then start:

- one atomic step: do it directly
- several independent parts: subagents in parallel
- a multi-step sequence: `delegated-execution`
- anything else: work through it in this session

When the user says "yolo", "just go", or "you decide", take your own recommendation on design choices without asking. Tests, `completion-gate`, `security-protocol`, and the confirmation before anything destructive still run in full.

## How to reply

- The first line is the answer, or the thing the user can do next.
- Say where things stand: what is done, what is left, what is unverified.
- Recommend one next step rather than a menu.
- Keep lists to five items.

The harness and the user's own instructions outrank everything in this file.
