---
name: quality-enforcement
description: Use when preparing code for commit, PR, or merge - covers linting, type safety, bundle budgets, coverage thresholds, complexity limits, dependency audit, and dead code detection
---

# Quality Enforcement

If a machine can check it, a machine checks it, in CI, on every PR. A quality check that is not enforced in CI does not exist. The failure this prevents: code landing with a check disabled, skipped, or quietly loosened to let it through.

## When to Use

Before:
- Committing code
- Opening a pull request
- Merging to trunk
- Releasing to production

## The Entry Protocol

Before any PR or merge:

1. **Lint:** zero errors, zero warnings
2. **Types:** zero type errors (strict mode)
3. **Tests:** all passing, coverage meets threshold
4. **Build:** clean build, no warnings
5. **Size:** bundle/binary within budget (if applicable)
6. **Deps:** no known vulnerabilities (critical/high)
7. **Complexity:** no functions exceeding complexity threshold

Done when every check that applies to this project has run and reported zero. A failing check means the code is not ready: fix the code, and the check stays.

## Quality Check Reference

### Check 1: Linting

```
Standard: Zero errors AND zero warnings
```

| Setting | Value | Rationale |
|---------|-------|-----------|
| Errors | 0 | Non-negotiable |
| Warnings | 0 | Warnings become errors you learn to ignore |
| Config committed | Yes | Consistent across all contributors |
| CI enforced | Yes | Local overrides are irrelevant |

**Warnings are tomorrow's errors.** Fix the warning, or change the rule in the committed config with the reason written beside it. A rule is turned off only when the team agrees it does not fit this project, and the change lives in the config file, not inline.

### Check 2: Type Safety

```
Standard: Zero type errors, strict mode enabled
```

**TypeScript:**
```json
{
  "compilerOptions": {
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "forceConsistentCasingInFileNames": true
  }
}
```

**Python:** mypy or pyright with strict mode.

| Anti-Pattern | Problem | Fix |
|-------------|---------|-----|
| `any` type | Disables type checking | Use specific types or generics |
| `// @ts-ignore` | Conceals real errors | Fix the type error |
| `# type: ignore` | Same problem | Fix the type error |
| Non-strict mode | False sense of safety | Enable strict from day one |

**`any` is a type error you chose not to resolve.** Every `any` weakens the type system for everything it touches.

### Check 3: Test Coverage

```
Standard: Coverage threshold that never decreases
```

| Metric | Floor | Target |
|--------|-------|--------|
| Line coverage | 80% | 90%+ |
| Branch coverage | 70% | 85%+ |
| New code coverage | 90% | 100% (aspire to) |

**Coverage thresholds are a ratchet.** They increase, never decrease. Configure CI to fail if coverage drops below the current level.

Coverage is necessary but not sufficient: high coverage with weak assertions proves less than lower coverage with rigorous ones. Coverage checks combine with TDD discipline (`ascension:test-first`).

### Check 4: Build

```
Standard: Clean build, zero warnings
```

- Build must complete successfully
- Zero compiler/build warnings
- Output matches expected structure
- No missing dependencies at build time

### Check 5: Bundle Size Budget (Frontend)

```
Standard: Total bundle size within defined budget
```

| Target | Budget | Tool |
|--------|--------|------|
| Initial JS (compressed) | <200KB | webpack-bundle-analyzer |
| CSS | <50KB | PurgeCSS check |
| Images | Optimized, WebP/AVIF | imagemin |
| Total page weight | <1MB | Lighthouse |

**Set the budget. Enforce in CI.** When budget is exceeded, analyze the bundle contents and eliminate or split.

### Check 6: Dependency Audit

```
Standard: Zero critical or high vulnerabilities
```

| Language | Command | CI Integration |
|----------|---------|---------------|
| JavaScript | `npm audit --audit-level=high` | Fail on high+ |
| Python | `pip-audit` or `safety check` | Fail on high+ |
| Go | `govulncheck ./...` | Fail on any |
| Rust | `cargo audit` | Fail on any |

**Also verify:**
- No unnecessary dependencies (is every dep actually used?)
- No duplicate dependencies (different versions of same package)
- Dependencies are maintained (last update within 12 months)

### Check 7: Complexity Metrics

```
Standard: No function exceeds complexity threshold
```

| Metric | Threshold | Tool |
|--------|-----------|------|
| Cyclomatic complexity | <10 per function | ESLint (complexity rule), radon, gocyclo |
| Function length | <50 lines | Linter rules |
| File length | <400 lines | Linter rules |
| Parameters | <5 per function | Linter rules |

**When complexity exceeds threshold:** refactor. Extract functions. Simplify conditionals. The threshold stays where it is.

### Check 8: Dead Code

```
Standard: No unused exports, variables, or dependencies
```

| What | Tool |
|------|------|
| Unused exports | ts-prune, knip |
| Unused dependencies | depcheck, knip |
| Unused variables | Linter (no-unused-vars) |
| Unreachable code | Linter, type checker |

Dead code is misleading code. It implies something depends on it. Remove it.

## CI Pipeline Template

```yaml
# Minimum quality enforcement pipeline
quality-checks:
  steps:
    - name: Lint
      run: npm run lint
    - name: Type Check
      run: npm run typecheck
    - name: Test
      run: npm test -- --coverage
    - name: Coverage Check
      run: check-coverage --threshold 80
    - name: Build
      run: npm run build
    - name: Bundle Size
      run: bundlesize
    - name: Audit
      run: npm audit --audit-level=high
```

All checks run on every PR. All must pass before merge.

## Guardrails

- When a check fails, fix the code and leave the check in place; CI runs on every merge, including the one you tested locally.
- Lint rule changes live in the committed config with their rationale written beside them.
- A `@ts-ignore` or `# type: ignore` carries a linked issue; where the type error can be fixed, fix it instead.
- The coverage threshold in config only moves up.
- Replace `any` with a specific type or generic as it appears.
- Investigate bundle growth against the budget before it lands; features replace or split, they do not only add.
- Count warnings as failures.
- Treat an audit finding in a dev dependency as live: dev dependencies run in CI, and CI holds the secrets.
- Delete dead code; git history keeps it if it is ever needed again.

Stop and fix the check before writing more code when you notice: a lowered threshold in config, a skipped or overridden CI run on a merge, or an inline rule disable with no rationale.

## Related

`ascension:test-first` supplies the tests these checks count. `ascension:completion-gate` is where the check output becomes the evidence for a claim of done. `ascension:project-bootstrap` configures the checks when the project is set up.
