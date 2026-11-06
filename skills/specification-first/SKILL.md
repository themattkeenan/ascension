---
name: specification-first
description: Use when building any feature, API, module, or system before writing implementation code - produces a structured behavior specification defining inputs, outputs, constraints, edge cases, and acceptance criteria as the authoritative artifact
---

# Specification First

## Overview

Author the specification before the implementation. The specification is the source of truth: it defines what the system does, and code is written to satisfy it, never the reverse. Without one the implementation is a guess at intent, and building the wrong thing is the failure this skill prevents.

## When to Use

- Developing a new feature or module
- Creating an API endpoint
- Constructing a data pipeline
- Building any system with defined inputs and outputs
- The user says "build me X" or "create Y"

Skip, after confirming with the user, for:
- Configuration changes
- Single-line defect fixes with unambiguous behavior
- Documentation updates

## The Entry Protocol

Before writing any implementation code:

1. **Identify** the inputs.
2. **Define** the expected outputs.
3. **Constrain**: what are the boundaries and limitations?
4. **Edge**: what happens at the edges? (empty, null, maximum, concurrent)
5. **Accept**: what criteria demonstrate correctness? Each one must yield at least one test.
6. **Write** the specification document using the template below.
7. **Approve**: obtain the user's confirmation.
8. **Implement**, calling the Skill tool with "ascension:test-first" so the acceptance criteria become the first tests.

Done when the approved specification exists and every acceptance criterion in it can be turned into a test.

## Specification Document Template

```markdown
# [Feature Name] Specification

## Purpose
[Single sentence: what this does and why it exists]

## Inputs
| Input | Type | Required | Constraints | Default |
|-------|------|----------|-------------|---------|
| [name] | [type] | [yes/no] | [validation rules] | [default] |

## Outputs
| Scenario | Output | Shape |
|----------|--------|-------|
| Success | [what is returned] | [type/structure] |
| Invalid input | [error response] | [error shape] |
| Not found | [404 response] | [error shape] |

## Behavior

### Standard Path
1. [Step-by-step behavior for normal usage]
2. [Include state mutations, side effects]

### Edge Cases
| Case | Input | Expected Behavior |
|------|-------|-------------------|
| Empty input | "" | Return validation error |
| Maximum length | 10000 chars | Truncate to 5000 |
| Concurrent access | 2 simultaneous requests | Serialize second request |
| [case] | [input] | [behavior] |

## Acceptance Criteria
- [ ] [Criterion 1 - testable, specific]
- [ ] [Criterion 2 - testable, specific]
- [ ] [Criterion 3 - testable, specific]

## Explicitly Excluded (YAGNI)
- [Feature intentionally omitted]
- [Optimization intentionally deferred]
```

## Relationship with Test-First Development

**Specification -> Tests -> Code -> Verify against Specification.** When verification finds a criterion unmet, revise the specification or the implementation and run it again.

The specification's acceptance criteria map directly to test cases. Each criterion generates at least one test. If you cannot derive a test from a criterion, the criterion is too vague -- refine the specification.

## Specification Depth Levels

| Level | When Appropriate | Scope |
|---|---|---|
| **Minimal** | Simple feature, obvious behavior | Purpose + Inputs + Outputs + 3 acceptance criteria |
| **Standard** | Most features | Full template above |
| **Exhaustive** | Complex systems, public APIs, multi-tenant | Full template + sequence diagrams + state machines + error taxonomy |

Scale the specification to the complexity. A utility function warrants a minimal spec. An authentication system demands an exhaustive one.

## Guardrails

- Obtain approval of the specification before the first line of implementation; a specification written afterwards describes what was built, not what was needed.
- Map acceptance criteria to test cases one-to-one.
- Update the specification when requirements change, before the code changes.
- Resolve every edge case in the specification; one that is deliberately out of scope goes under Explicitly Excluded rather than being left for later.
- Verify every criterion passes before declaring completion.
- When the tests feel like enough on their own, write the specification anyway: tests verify behaviour, the specification decides which behaviour to build, and its criteria feed the tests.
- When the user's description seems complete, translate it into behaviour before coding: users describe goals; the specification names inputs, outputs and edges.

## Related

- `ascension:intent-discovery` -- produces the design this specification is written from
- `ascension:task-planning` -- produces the implementation plan from the specification
- `ascension:completion-gate` -- verifies the result against the acceptance criteria
