---
name: merge-protocol
description: Use when implementation is finished, tests are green, and you need to decide how to land the work - presents structured integration paths for local merge, pull request, deferral, or abandonment
---

# Merge Protocol

## Overview

Coordinate the final step of development: moving finished work into the mainline or disposing of it cleanly. Validate first, present the four paths, execute the one chosen, then tidy up. Nothing integrates until the suite is green on the current commit.

**Announce at start:** "I'm using the merge-protocol skill to finalize this work."

## When to Use

- All planned implementation work is done and committed
- The test suite runs green on the current branch
- Review feedback (if solicited) has been resolved
- Following delegated-execution or task-runner completion
- When a workspace-isolation branch needs teardown

## The Workflow

### Phase 1: Validate the Suite

**Run the full test suite before anything else:**

```bash
# Execute the project's test runner
npm test / cargo test / pytest / go test ./...
```

**On failure:**
```
Suite failures detected (<N> failing). Resolution required before proceeding:

[Display failing tests]

Integration cannot continue until the suite is green.
```

Stay in this phase until the suite is green on the current commit. Results from before the last commit are stale.

### Phase 2: Identify the Trunk Branch

```bash
# Determine the ancestor branch
git merge-base HEAD main 2>/dev/null || git merge-base HEAD master 2>/dev/null
```

Or confirm: "This branch diverged from main — correct?"

Done when the trunk branch is named.

### Phase 3: Offer Integration Paths

Present exactly these four choices:

```
Work is complete and verified. Select an integration path:

1. Merge into <trunk> locally
2. Push and open a Pull Request
3. Leave the branch untouched (I'll handle it myself)
4. Abandon this work entirely

Which path?
```

**Keep it terse** — no extra commentary. Done when the user has picked a number.

### Phase 4: Execute the Selected Path

#### Path 1: Local Merge

```bash
# Switch to trunk
git checkout <trunk>

# Sync with remote
git pull

# Integrate the feature branch
git merge <feature-branch>
# If this stops with conflicts, call the Skill tool with "ascension:merge-conflicts"

# Validate the merged state
<test command>

# Remove the merged branch
git branch -d <feature-branch>
```

Then: Proceed to Phase 5 (Worktree Teardown)

#### Path 2: Pull Request

```bash
# Push the branch upstream
git push -u origin <feature-branch>

# Open the PR
gh pr create --title "<title>" --body "$(cat <<'EOF'
## Summary
<2-3 bullets describing the changes>

## Verification Steps
- [ ] <checklist items>
EOF
)"
```

Report the PR URL. The branch is live until the PR merges, so the worktree stays in place for review fixes.

Then: Proceed to Phase 5 (Worktree Teardown)

#### Path 3: Defer

Report: "Branch <name> preserved. Worktree remains at <path>."

The worktree stays in place.

#### Path 4: Abandon

**Require explicit confirmation:**
```
Permanent deletion warning:
- Branch <name>
- All commits: <commit-list>
- Worktree at <path>

Type 'abandon' to proceed.
```

Wait for the exact word.

After confirmation:
```bash
git checkout <trunk>
git branch -D <feature-branch>
```

Then: Proceed to Phase 5 (Worktree Teardown)

### Phase 5: Worktree Teardown

**Applies to Paths 1 and 4**, the two paths on which the branch is finished:

Detect if the current branch lives in a worktree:
```bash
git worktree list | grep $(git branch --show-current)
```

If so:
```bash
git worktree remove <worktree-path>
```

**Paths 2 and 3:** the worktree stays intact; the branch is still live.

Done when `git worktree list` no longer shows the path (Paths 1, 4) or the user has been told where it remains (Paths 2, 3).

### Phase 6: Close the Phase

Landed work is a phase boundary. Two calls, in order:

1. If the work surfaced something a future session would otherwise rediscover — a gotcha, a correction from the user, a convention nothing records — call the Skill tool with "ascension:knowledge-capture".
2. Call the Skill tool with "ascension:context-economy" to decide whether the next piece of work starts in this session or a fresh one.

## Decision Matrix

| Path | Merges | Pushes | Keeps Worktree | Removes Branch |
|------|--------|--------|----------------|----------------|
| 1. Local merge | Yes | No | No | Yes |
| 2. Pull request | No | Yes | Yes | No |
| 3. Defer | No | No | Yes | No |
| 4. Abandon | No | No | No | Yes (force) |

## Guardrails

- Run the suite on the current commit before presenting paths; a green run from before the last commit does not count.
- Present exactly the four paths and let the user pick. An open "what now?" stalls.
- Validate the merged state before deleting the merged branch.
- Delete a branch only on the user's explicit confirmation; on Path 4, the typed word "abandon".
- Force-push only when the user asks for it.
- Remove the worktree in the same turn the branch is merged or abandoned; orphaned worktrees pile up and confuse later sessions.
- Tear down worktrees only for Paths 1 and 4.

## Related

Invoked as the final step of `ascension:delegated-execution` and `ascension:task-runner`. Tears down the worktree `ascension:workspace-isolation` created.
