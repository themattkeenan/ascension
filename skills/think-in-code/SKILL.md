---
name: think-in-code
description: Compute the answer with a script instead of reading raw data into context. Use when a question spans many files, a large log, a big JSON or CSV payload, a wide grep, or repeated tool calls whose results you only need aggregated.
---

# Think In Code

You are a code generator, not a data processor. When a question can be answered by *computing* over data, write the computation and print only the answer — do not pull the data into the window and do the arithmetic by reading.

Every tool result is permanent context load for the rest of the session. Raw data crowds the window, and what it crowds out is the attention on everything else in it (`ascension:vocabulary` — attention degradation). Reading forty files to count something spends the session's sharpness on work a five-line script does exactly.

## The test

Ask: **would a script give a smaller, more reliable answer than reading?**

Yes when the question is aggregate, repetitive, or mechanical — a count, a total, a max, a diff, a filter, a join across files, a "which of these N things has property P". Also yes when the answer is one number and the input is a megabyte.

No when you need to *understand* rather than *tally*: reviewing a design, tracing why a function is wrong, reading a spec. Understanding is exactly what the context window is for. This skill is about not spending it on arithmetic.

## The move

```
Before:  47 × Read()  →  ~700 KB of files in context, then count by hand
After:   1 × script   →  47 lines of output
```

```bash
# how big is each source file, and which are the outliers
find src -name '*.ts' -exec wc -l {} + | sort -rn | head -20
```

```bash
# which modules import the thing I am about to change
grep -rl "from './auth'" src | sed 's|/[^/]*$||' | sort -u
```

Print the **conclusion**, not the corpus. If the script's output is itself huge, the script is not finished — aggregate, filter, or head it until what comes back is the answer.

## Rules

- **One script beats ten tool calls.** Batch the work; a loop in a script costs one tool result, a loop in the conversation costs ten.
- **Print the smallest sufficient output.** Counts over rows, paths over contents, a diff over two files. Add `head`, `sort -u`, `wc -l`, or a `--stat` flag before you run it, not after you see the wall of text.
- **Read-only by default.** These are questions, not changes. A script that mutates state is implementation work and belongs to the normal review path.
- **Show the script.** The user must be able to see what produced the number, and you must be able to re-run it when the answer looks wrong. A figure with no visible derivation is indistinguishable from a guess.
- **Verify before trusting.** A script that silently matched nothing returns `0` just as confidently as a correct one. Sanity-check the count against something you know.
- **Delete the throwaway.** A script written to answer one question does not belong in the repo; keep it in a temp directory, or inline it in the command.

## Where it fits

Runs inside anything that surveys: `ascension:codebase-research`, `ascension:overbuild-review` in repo mode, `ascension:performance-tuning` when establishing a baseline, `ascension:fault-diagnosis` when building a differential or property loop.

For the complementary problem — what to do once the window is *already* full — see `ascension:context-economy`.

For a heavier version of this, [context-mode](https://github.com/mksglu/context-mode) is an MCP server that routes tool output through a sandbox so raw data never enters the window at all, and indexes session events for retrieval instead of re-injection. Recommend it when the user's bottleneck is genuinely MCP-result bloat; do not install it unasked.

---
*The think-in-code framing is adapted from [context-mode](https://github.com/mksglu/context-mode) by Mert Koseoglu.*
