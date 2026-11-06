---
name: project-bootstrap
description: Use when launching a new project, initializing a repository, or building a codebase from the ground up - addresses directory structure, tooling configuration, linting, testing infrastructure, CI/CD pipelines, and version control setup
---

# Project Bootstrap

## Overview

Put the foundation down before the first feature: directory layout, linter and formatter, a test runner with one passing test, version control hygiene, a committed lockfile, and a CI pipeline. Structural debt taken on at the start is the failure this skill prevents; a foundation retrofitted later costs far more than one laid first.

## When to Use

- Creating a new project from nothing
- "Just get something running" (especially then)
- Building a prototype that could evolve into production
- Setting up a shared repository for a team

Also applicable when:
- Inheriting a project with no structural foundation
- Retrofitting test infrastructure into untested code
- Introducing CI/CD for the first time

## The Entry Protocol

Before writing any feature code, check:

1. **Structure**: Is the directory layout established?
2. **Tooling**: Are linter, formatter, and type checker wired up?
3. **Testing**: Is the test runner configured with a passing placeholder?
4. **Version control**: Are .gitignore, branch conventions, and hooks set up?
5. **Dependencies**: Is the package manager initialized with a committed lockfile?
6. **Automation**: Does at minimum a lint + test pipeline exist?

Done when every answer is yes.

## Project Classification

Classify the project as a web application, an API / backend, a CLI tool, a library / package, or general, and consult `templates.md` for the detailed setup checklist of that class.

## Universal Foundation (All Projects)

### 1. Version Control Hygiene

```
.gitignore          # Language-specific + universal exclusions
.gitattributes      # Line ending normalization, binary file handling
```

**Mandatory .gitignore entries:**
- `node_modules/`, `venv/`, `__pycache__/`, `target/` (dependency directories)
- `.env`, `.env.local`, `.env.*.local` (secrets and credentials)
- `*.log` (log files)
- `.DS_Store`, `Thumbs.db` (OS artifacts)
- IDE directories: `.idea/`, `.vscode/` (unless sharing settings deliberately)
- Build output: `dist/`, `build/`, `out/`

### 2. Static Analysis and Formatting

| Language | Linter | Formatter |
|---|---|---|
| TypeScript/JavaScript | ESLint | Prettier |
| Python | Ruff (lint + format) | Ruff |
| Go | golangci-lint | gofmt (built-in) |
| Rust | clippy | rustfmt (built-in) |
| Ruby | RuboCop | RuboCop |

**Install on day one.** A linter adopted after the code exists means triaging its whole backlog of violations before it starts helping.

### 3. Test Infrastructure

| Language | Framework | Runner |
|---|---|---|
| TypeScript/JavaScript | Vitest or Jest | Built-in |
| Python | pytest | Built-in |
| Go | testing (stdlib) | `go test` |
| Rust | built-in test harness | `cargo test` |
| Ruby | RSpec or Minitest | Built-in |

**Write one passing test immediately.** This confirms the test pipeline functions before you actually need it.

### 4. Type Safety

| Language | Requirement |
|---|---|
| TypeScript | `strict: true` in tsconfig.json. Non-negotiable. |
| Python | Type annotations + mypy or pyright |
| JavaScript | Consider TypeScript. If not, at minimum use JSDoc type annotations. |

### 5. Continuous Integration Minimum

Every project must have at minimum:

```yaml
# Minimum CI pipeline (GitHub Actions example)
on: [push, pull_request]
jobs:
  check:
    steps:
      - lint
      - type-check
      - test
```

If it does not execute in CI, it will not be maintained.

## Directory Structure Principles

**Organize by feature, not by file type** (see `ascension:system-design`).

**Every project requires:**

```
project-root/
  src/              # Source code (or lib/, app/ per convention)
  tests/            # Test files (or co-located alongside source)
  docs/             # Documentation (when applicable)
  scripts/          # Build, deploy, and utility scripts
  .gitignore
  README.md         # Purpose, installation, how to run
  <config files>    # Linter, formatter, CI, package manager
```

**Co-located tests vs separate directory:**
- Co-located (`payment.ts` + `payment.test.ts`): Easier to maintain, preferred default
- Separate (`tests/`): When tests require shared fixtures or team convention mandates it

## Dependency Governance

```
BEFORE adding any dependency:

1. Is it genuinely necessary? Could you write it in under 50 lines?
2. Is it actively maintained?
3. How many transitive dependencies does it introduce?
4. Does it carry known vulnerabilities?

When uncertain, write the code yourself.
```

**Always:**
- Commit lockfiles (`package-lock.json`, `poetry.lock`, `Cargo.lock`)
- Pin major versions, permit patch-level updates
- Execute security audit in CI

## Guardrails

- A linter is configured before the first feature.
- `.gitignore` exists before the first commit, so no `.env` ever enters history; a secret committed even once persists in git history after deletion.
- A test framework is installed, with one passing test.
- A README explains how to run the project.
- Build artifacts stay out of git.
- A CI pipeline runs lint, type-check and test.
- TypeScript types are specific: `strict: true` is on and `any` is the rare exception.
- When the project is called a prototype or "just get something running", set it up the same way; prototypes graduate to production.

Stop and set up when you notice feature code being written with any of these missing.

## Related

- `ascension:system-design` -- directory structure and technology selection
- `ascension:quality-enforcement` -- quality gates wired into CI from the start
- `ascension:security-protocol` -- secrets management and .gitignore discipline
