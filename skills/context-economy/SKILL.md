---
name: context-economy
description: Use when a task has just landed or a plan was just approved, when the user asks for a new chat, a handoff, or a summary of where things stand, when you catch yourself re-reading the same files or repeating a correction, or when work must move to another session, directory, harness, or person. Decides continue, clear, hand off, subagent, or compact, and writes the handoff.
---

# Context Economy

A **phase** is a chunk of work inside a session: the grilling, the implementation, the QA. A phase ends when you think *"right, that's done."*

The **phase boundary** is the gap between two phases, and it is the only place this decision belongs. Mid-phase there is nothing to decide: continue, or split the remaining work into subagents. Compacting mid-phase loses the thread.

## The smart zone

Early in a session the agent is sharp. As the session grows, each token's attention spreads across more competitors, and the agent drifts into a **dumb zone**: it forgets instructions from twenty turns ago, repeats corrections it already made, contradicts context it can still see. Same model, same prompt — just more context.

The decline is gradual and unannounced, so the usual response is to re-explain, which adds context and makes it worse. Where the zone begins varies by model and by what the context holds; the signs above are the measurement, not a token count.

The smart zone is a **budget**, and it does not track the context-window limit — quality falls off long before the harness refuses to continue. Plan around the smart zone, not the window, and give each task the sharp part of a session.

## The five options

| Option | What it does |
|---|---|
| **Continue** | Stay in the session. No context switch at all. |
| **Clear** | Empty the window and start from nothing. |
| **Handoff** | Write a portable markdown file and seed a session anywhere with it. |
| **Subagent** | Send the task to its own window and get a report back. |
| **Compact** | Compress this context and seed a fresh session with the summary. |

## The tree

Work top to bottom at the boundary. The first **yes** wins.

**1. Can you continue in this session?** Two things make it a yes: the next phase needs this one as a **primary source**, or enough smart zone remains for the next phase to fit. Grilling → implementation is the standard yes — the implementation wants the reasoning verbatim, not a summary of it. Continue costs nothing and loses nothing, so rule it out first.

**2. Is the context irrelevant to what comes next?** If the exploration, the decisions, and the dead ends are all disposable, **clear**. It is the cheapest move on the board and it is not terminal: the old session stays resumable.

Getting this wrong is one-way. Clear a *relevant* context and you lose the **why** behind what you built; reading the diff back does not return it.

**3. Do you need to hand off?** Handoff is narrow. You need it only when something is **travelling**: a new harness, a new directory or repo, a colleague, or a side task forked mid-phase. That list is the whole clause. What a handoff buys is portability. See [Writing a handoff](#writing-a-handoff).

**4. Can the task run unattended?** If it is scoped tightly enough to run with nobody steering, send it to a **subagent** and leave this session untouched. Automated review is the standard case. See `ascension:agent-messaging` for how to write the prompt.

**5. Otherwise, compact.** Relevant context, same harness, same directory, and you need to stay in the loop. Pass an instruction with it (*"compact — we're going to QA this area next"*) so the summary keeps what the next phase needs.

Compact is the **default, not the first reach**. It sits at the bottom because the four questions above it are cheaper or more precise. Starting here produces a fresh session that is confidently wrong about a decision the summary flattened.

## Primary and secondary sources

Every move except **Continue** turns a **primary source** (the session as it happened) into a **secondary source** (a summary of it).

| Source | Information | Noise | Room to move |
|---|---|---|---|
| Primary (continue) | Full | Lots | Little |
| Secondary (compact, handoff) | Lossy | Less | Lots |

This is why question 1 comes first: you pay the lossiness only when staying costs more than it saves. Whichever move you make, leave a **pointer back to the primary source** — the transcript path, the branch, the design doc — so the lossiness stays recoverable.

## Writing a handoff

Write the document so a fresh agent, with none of this context, can continue the work. Save it to the OS temp directory, **not** the workspace, unless the user asks otherwise.

Include:

- **Goal** — what the next session is for. If the user said, use their words.
- **State** — what is done, what is in flight, what is known broken.
- **Decisions and their reasons** — the *why*, which is the part no diff recovers.
- **Pointers, not copies** — reference specs, plans, ADRs, issues, commits, branches, and diffs by path or URL. Do not restate what those artifacts already hold.
- **Next steps** — the concrete first actions.
- **Suggested skills** — which skills the next agent should call the Skill tool for.

Redact secrets and personal data: write `<REDACTED>` in their place.

## These are judgement calls

None of the five questions is objective, and the same boundary can go two ways on two days. The value is in asking them **in order**, at the boundary rather than in the middle of the work.

---
*Adapted from [mattpocock/skills](https://github.com/mattpocock/skills) and [mattpocock/dictionary-of-ai-coding](https://github.com/mattpocock/dictionary-of-ai-coding) (MIT).*
