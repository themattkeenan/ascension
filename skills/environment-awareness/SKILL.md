---
name: environment-awareness
description: Use when starting a session, running shell commands, installing packages, or diagnosing platform-specific failures - detects OS, shell, runtime, package manager, toolchain, and which outbound research channels the harness can actually reach
---

# Environment Awareness

Running Linux commands on Windows, using npm in a pnpm project, ignoring the active virtual environment: each wrong command wastes a turn, and some leave broken state behind. One probe at the start of a session makes every later command land.

Know the OS, the shell, and the package manager before the first command. Everything else here is how.

## When to use

**At session start:** the first interaction in a conversation, a switch to a different repository, or entering a container, VM, or remote machine.

**Also when:**
- A command fails with "not found" or "not recognized"
- Installing dependencies or running build scripts
- Writing platform-specific code: file paths, process management, networking
- Diagnosing a "works on my machine" problem
- A skill is about to require external research (`ascension:reference-engine`, `ascension:github-search`, `ascension:design-research`, `ascension:primary-research`): see **Outbound reach** below

A casual mention of the OS from the user confirms the OS only; the shell and toolchain still need probing. CI runners are a separate environment from the laptop and get their own probe.

## Before the first command

1. **Platform**: OS and architecture confirmed.
2. **Shell**: you know which shell interprets your commands, and which shell the user's terminal runs (they often differ).
3. **Package manager**: lockfiles checked.
4. **Runtimes**: the language runtimes the project needs are known to be present.
5. **Virtual environment**: active venvs and version managers identified.

Done when all five are known. Any unknown: probe it first, then run the command.

## Detection checklist

Run this sequence once per session. Results inform every subsequent command.

### 1. Operating system

| Method | Works on | Command |
|--------|----------|---------|
| Environment variable | Claude Code / Node | Check `process.platform` in tool context |
| uname | Linux, macOS, Git Bash | `uname -s` |
| System info | Windows (any shell) | `systeminfo` or `ver` |

**Key distinctions:**
- `win32` = Windows (regardless of 32/64-bit)
- `darwin` = macOS
- `linux` = Linux or WSL (check further with `grep -i microsoft /proc/version` for WSL)

### 2. Shell

Windows alone has CMD, PowerShell, Git Bash, and WSL, so confirm the shell rather than assuming bash.

| Check | Command | Reveals |
|-------|---------|---------|
| Shell variable | `echo $SHELL` | Default shell (Unix) |
| PowerShell test | `$PSVersionTable` | PowerShell version |
| Process name | `echo $0` | Current shell (Unix) |
| COMSPEC | `echo %COMSPEC%` | CMD path (Windows) |

On Windows the agent's shell is often Git Bash while the user's terminal is PowerShell. Commands you give the user to copy match **their** shell.

### 3. Package manager

Check lockfiles first:

| Lockfile | Package manager |
|----------|----------------|
| `bun.lockb` or `bun.lock` | bun |
| `pnpm-lock.yaml` | pnpm |
| `yarn.lock` | yarn |
| `package-lock.json` | npm |

With no lockfile: the `packageManager` field in `package.json`, then a global install (`which pnpm || which yarn || which bun`), and npm only as the last resort.

For non-JS projects:

| File | Manager |
|------|---------|
| `Pipfile.lock` | pipenv |
| `poetry.lock` | poetry |
| `uv.lock` | uv |
| `requirements.txt` | pip |
| `go.sum` | go modules |
| `Cargo.lock` | cargo |
| `Gemfile.lock` | bundler |

### 4. Runtime versions

Probe on first use, not eagerly. Check only what the project needs.

```
node --version      # Node.js
python3 --version   # Python (python3, not python, on macOS/Linux)
go version          # Go
rustc --version     # Rust
java --version      # Java
ruby --version      # Ruby
```

### 5. Virtual environments

| Signal | Indicates |
|--------|-----------|
| `$VIRTUAL_ENV` is set | Python venv/virtualenv active |
| `$CONDA_DEFAULT_ENV` is set | Conda environment active |
| `.python-version` file | pyenv version pinned |
| `.nvmrc` or `.node-version` file | Node version pinned |
| `.ruby-version` file | rbenv/rvm version pinned |
| `.tool-versions` file | asdf version manager |

When a version manager is present, use its commands (`nvm use`, `pyenv shell`) rather than the global runtime.

### 6. Container detection

| Check | Inside container? |
|-------|-------------------|
| `/.dockerenv` exists | Docker |
| `grep -q container /proc/1/cgroup 2>/dev/null` | Docker/Podman |
| `$container` env var is set | Podman |
| `printenv KUBERNETES_SERVICE_HOST` | Kubernetes pod |

## System inventory

After platform and shell, discover what tools, databases, cloud CLIs, and services are installed. The inventory feeds planning: with SQLite but no PostgreSQL, or Docker but no Podman, `ascension:deployment-advisor` and `ascension:task-planning` recommend from what exists instead of guessing.

**Scope the checks to the project.** Git and the GitHub CLI always; database CLIs if the project touches data; cloud and container tools if it deploys; ffmpeg, jq, curl if it processes media or structured data. A static site needs no MongoDB probe.

**Databases:**

| Tool | Check | Notes |
|------|-------|-------|
| PostgreSQL | `psql --version 2>/dev/null` | Check for `pg_dump` too if backups matter |
| MySQL / MariaDB | `mysql --version 2>/dev/null` | MariaDB identifies itself in the version string |
| SQLite | `sqlite3 --version 2>/dev/null` | Often pre-installed on macOS and Linux |
| MongoDB | `mongod --version 2>/dev/null` | Also check `mongosh` for the modern shell |
| Redis | `redis-server --version 2>/dev/null` | Also check `redis-cli` |

**Cloud and hosting CLIs:**

| Tool | Check |
|------|-------|
| AWS CLI | `aws --version 2>/dev/null` |
| Google Cloud | `gcloud --version 2>/dev/null` |
| Azure CLI | `az --version 2>/dev/null` |
| Vercel | `vercel --version 2>/dev/null` |
| Supabase | `supabase --version 2>/dev/null` |
| Fly.io | `fly version 2>/dev/null` |
| Netlify | `netlify --version 2>/dev/null` |
| Railway | `railway --version 2>/dev/null` |

**Container tools:**

| Tool | Check |
|------|-------|
| Docker | `docker --version 2>/dev/null` |
| Docker Compose | `docker compose version 2>/dev/null` |
| Podman | `podman --version 2>/dev/null` |

**Developer tools:**

| Tool | Check |
|------|-------|
| Git | `git --version 2>/dev/null` |
| GitHub CLI | `gh --version 2>/dev/null` |
| curl | `curl --version 2>/dev/null` |
| jq | `jq --version 2>/dev/null` |
| ffmpeg | `ffmpeg -version 2>/dev/null` |

**Report concisely**, versions extracted, no raw output:

```
Available: PostgreSQL 16.2, Docker 27.1, gh 2.45, SQLite 3.43
Not found: Redis, AWS CLI, Podman
```

**Keep the probe green.** Redirect stderr and end the chain with `; true`, so a missing tool reads as information rather than a red error in the transcript:

```bash
psql --version 2>/dev/null; redis-server --version 2>/dev/null; mongod --version 2>/dev/null; true
```

Probe once and keep the results in working context; re-probe only when the user installs or connects something.

## Outbound reach

Whether this harness can reach outside the machine is an environment fact, like the shell. The research skills assume it: `ascension:reference-engine`, `ascension:github-search`, `ascension:design-research` and `ascension:primary-research` each require looking at real implementations, repositories, and documents before building. None of them can tell whether a channel exists. An agent that cannot reach a source and is still required to cite one tends to describe a library it never opened and report the step satisfied, and a fabricated reference is worse than none because the checklist now reads as complete.

So probe the channels once per session, before the first skill that needs them, and declare the tier.

| Channel | Probe | Unlocks |
|---------|-------|---------|
| Web search | A search tool in the tool list | `github-search` discovery, `design-research` marketplace survey |
| Web fetch | A fetch or read-URL tool | `primary-research` against docs, RFCs, changelogs |
| GitHub, authenticated | `gh auth status 2>/dev/null; true` | Private repos, issues and PRs, the authenticated rate limit |
| GitHub, anonymous | `curl -sI https://api.github.com/rate_limit` | Public repo reads, at the anonymous rate limit |
| Package registries | `npm view`, `pip index`, `cargo search` | Version and dependency facts without a web channel |
| Browser automation | Playwright, Puppeteer, or a browser tool present | Rendered pages, frontend loops for `fault-diagnosis` |
| MCP research servers | The connected server list | Whatever they expose; read their instructions |

Probe each channel on its own. The absence of a search tool says nothing about `curl` or `gh api`, which still reach primary sources by known URL.

| Tier | Available | How the research skills run |
|------|-----------|-----------------------------|
| **Full** | search + fetch + authenticated `gh` | As written |
| **Fetch-only** | fetch, no search | You can verify a URL but not discover one. Ask the user for candidates, or enumerate through registry CLIs, and say that discovery was narrow |
| **Shell-only** | `curl`, `gh`, registry CLIs | Primary sources reachable by known URL. A marketplace survey is out; `design-research` asks the user for reference links instead |
| **Sealed** | no outbound access | The research skills cannot run. Say so, name what you would have checked, and have the user supply references or waive them explicitly |

**Declare the tier in one line before invoking a research skill**: `Research tier: fetch-only, no search channel; discovery will be narrow.` The user can then hand you links instead of watching you guess. A claim you did not fetch is a lead to verify, not a finding. A declined permission or a blocked channel is a finding to report and adapt to, not something to route around. Rate limits are a budget: an anonymous GitHub quota will not cover `github-search`'s multi-channel minimum, so authenticate or spend it deliberately. Re-probe when the user connects an MCP server or a CLI login succeeds; the inventory is stale from that moment.

## Platform-specific command mappings

| Operation | Linux/macOS | Windows CMD | Windows PowerShell | Git Bash on Windows |
|-----------|-------------|-------------|-------------------|-------------------|
| List files | `ls -la` | `dir` | `Get-ChildItem` | `ls -la` |
| Find process | `ps aux \| grep` | `tasklist` | `Get-Process` | `ps aux \| grep` |
| Set env var | `export VAR=val` | `set VAR=val` | `$env:VAR = "val"` | `export VAR=val` |
| Null device | `/dev/null` | `NUL` | `$null` | `/dev/null` |
| Path separator | `:` | `;` | `;` | `:` |
| Delete file | `rm file` | `del file` | `Remove-Item file` | `rm file` |
| Find files | `find . -name` | `dir /s /b` | `Get-ChildItem -Recurse` | `find . -name` |
| Check port | `lsof -i :PORT` | `netstat -an` | `Get-NetTCPConnection` | `netstat -an` |

## When to probe

| Category | Action | Reason |
|----------|--------|--------|
| OS | Always probe | Affects every command's syntax |
| Shell | Always probe | Determines quoting, piping, redirection |
| Package manager | Always probe | The wrong manager corrupts the lockfile and breaks CI |
| Runtime versions | Probe on first use | Only matters when running that runtime |
| Virtual environments | Probe when relevant | The wrong env installs to the wrong location |
| Container | Probe when behaviour is unexpected | Containers lack many host tools |
| Outbound reach | Probe before the first research skill | Decides whether the research skills can run as written |

## Guardrails

- Confirm the shell as well as the OS. "Windows" can mean CMD, PowerShell, Git Bash, WSL, or Cygwin, and they quote, pipe, and redirect differently.
- Confirm the lockfile before any install command. A failed `rm -rf` with the wrong path syntax can still delete data, and a wrong-manager install leaves a corrupted lockfile; probing first is cheaper than recovering.
- Probe each outbound channel separately, and declare the research tier before a research skill runs rather than supplying a reference from recollection.
- Stop and probe when you notice yourself about to use `/dev/null`, `~`, `python`, GNU-only `grep` flags, or `npm install` without having confirmed the shell, OS, or lockfile they depend on.

## Related

`ascension:project-bootstrap` runs this detection during setup. `ascension:fault-diagnosis` treats environment mismatch as a common root cause. `ascension:deployment-advisor` consumes the system inventory. The research skills consume the outbound-reach tier.
