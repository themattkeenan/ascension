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

## Size the task, then split it

Size every task before starting and run it on the matching split. The most capable model does the thinking at the edges; cheaper models do the bulk in subagents.

| Size | Split | Who does what |
|---|---|---|
| Atomic: a question, a lookup, a one-file edit | direct | This session does it. |
| Small: fits one session and needs no plan document | **90-10** | An `opus` subagent scopes it and returns the tasks, each with the model to run it on. Subagents on those models do the tasks. This session does the last 10: the final review (read the diff, run the check), then the report. |
| Large: needs a plan, tickets, or more than one session | **10-80-10** | This session does the first 10: settle the design, write the plan and the briefs. Subagents do the 80, in parallel where the parts are independent, through `delegated-execution` where they are a sequence. This session does the last 10: review the whole result, integrate, run `completion-gate`. |

Name the model on every dispatch; an omitted model inherits this session's, usually the most expensive one.

- `haiku`: searching, reading, and transcription from a plan that already contains the code
- `sonnet`: the default builder
- `opus`: scoping a small task, multi-file integration, debugging, and review of a single task
- the most capable model (`fable` today): the 10s only. When this session runs on a lesser model, dispatch the final review to the most capable one.

Keep the 90 or the 80 in this session only when the brief would mostly retell what this session already knows.

## "Use ascension"

Say where the task lands on the map, its size and its split, with one line of reason, then start.

When the user says "yolo", "just go", or "you decide", take your own recommendation on design choices without asking. Tests, `completion-gate`, `security-protocol`, and the confirmation before anything destructive still run in full.

## How to reply

- The first line is the answer, or the thing the user can do next.
- Say where things stand: what is done, what is left, what is unverified.
- Recommend one next step rather than a menu.
- Keep lists to five items.

The harness and the user's own instructions outrank everything in this file.
