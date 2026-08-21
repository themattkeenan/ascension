---
name: grilling
description: Interview the user in rounds to settle every open decision on a plan, design, or idea. Use before designing or building anything non-trivial, when the user wants their thinking stress-tested, or when another skill needs the round/frontier interview.
---

# Grilling

Interview the user relentlessly until you reach a shared understanding. Map the work as a **design tree**: every decision branches into the decisions that hang off it.

Work the tree in **rounds**. The **frontier** is every decision whose prerequisites are already settled — the questions you can ask *now* without guessing at answers you have not heard yet. Ask the whole frontier in one round: number each question and give your recommended answer. Then wait.

Format a round like this:

```
❓ **Q1** — **<question title>**: <question body; may be several paragraphs, may offer labelled choices>

➡️ <your recommended answer, and why>

---

❓ **Q2** — **<question title>**: <question body>

➡️ <your recommended answer, and why>
```

Each round of answers reshapes the tree: settled decisions push the frontier outward and unblock questions that depended on them. Recompute the frontier and ask the next round. A question whose answer depends on another question still open *in this round* belongs to a **later** round.

## Facts are yours; decisions are theirs

Finding **facts** is your job, never the user's. When a frontier question needs a fact from the environment — what the config already sets, which library is installed, how the existing module is shaped — go get it. Dispatch a subagent (`ascension:codebase-research`, `ascension:github-search`, `ascension:primary-research`) rather than asking a question you could answer yourself.

Do not block on it. A running investigation is an unsettled prerequisite, so only the questions *downstream* of it wait; ask the rest of the frontier now.

The **decisions** are the user's. Put each one to them and wait for the answer.

## Sizing the rounds

One question is a round if the tree is that narrow. Twelve is a round if twelve decisions are genuinely independent. A grilling session that runs very long usually means the scope was too big — say so and offer to split it rather than grinding through.

## Done

The session ends when the frontier is empty: every branch visited, nothing left silently assumed. Do not act on the result until the user confirms you have reached a shared understanding.

While grilling inside a repository, also call the Skill tool with "ascension:domain-modeling" so the terms you settle land in `CONTEXT.md` and the hard-to-reverse choices land as ADRs as they crystallise.

---
*Adapted from [mattpocock/skills](https://github.com/mattpocock/skills) (MIT).*
