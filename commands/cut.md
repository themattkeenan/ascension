---
description: "Find over-engineering and report what to cut — a diff, the whole repo, or the deferred-shortcut debt ledger"
disable-model-invocation: true
---

Invoke the ascension:overbuild-review skill and follow it exactly as presented to you.

Pick the mode from what the user passed:

- a ref, `diff`, or nothing while a branch has changes → **diff** mode against that fixed point
- `repo`, `audit`, or `all` → **repo** mode
- `debt` or `ledger` → **debt** mode

If nothing was passed and there is no obvious diff, ask which of the three they want rather than guessing.
