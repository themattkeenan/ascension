---
name: merge-conflicts
description: Resolve an in-progress git merge or rebase conflict by tracing each side's intent. Use when a merge, rebase, or cherry-pick has stopped with conflicts.
---

# Merge Conflicts

Resolve by **intent**, not by picking lines. A hunk is two people's intentions colliding; the resolution is the code that honours both, which is often neither side verbatim.

1. **See the current state.** What operation is in flight (`git status`), which commits are involved (`git log --oneline --left-right MERGE_HEAD...HEAD`), and which files conflict.

2. **Find the primary source for each side.** Read the commit messages. Check the PRs and the originating issues. Understand *why* each change was made before touching a line. A hunk you resolve without knowing both intents is a guess.

3. **Resolve each hunk.** Preserve both intents where they can coexist. Where they genuinely cannot, pick the one matching the merge's stated goal and note the trade-off out loud. Do not invent new behaviour while resolving — a merge is not the place to redesign.

4. **Run the project's automated checks.** Typecheck, then tests, then format. Fix what the merge broke. `ascension:completion-gate` applies: read the output, do not assume it.

5. **Finish the operation.** Stage everything and commit; if rebasing, continue until every commit is applied.

**Always resolve; never `--abort`.** Aborting throws away the understanding you just built and leaves the same conflict waiting. If the merge genuinely should not proceed, say so and let the user make that call.

---
*Adapted from [mattpocock/skills](https://github.com/mattpocock/skills) (MIT).*
