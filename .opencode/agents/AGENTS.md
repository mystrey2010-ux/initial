# opencode Agent Instructions

## Goal
Deliver clean, maintainable, and testable code following Clean Code principles (Robert C. Martin).
Applies to all projects — primarily **Python** and **Bash**.

---

## Naming Conventions

| Context | Python | Bash |
|---|---|---|
| Variables | `snake_case` | `snake_case` |
| Functions | `snake_case` | `snake_case` |
| Classes | `PascalCase` | N/A |
| Constants | `UPPER_SNAKE_CASE` | `UPPER_SNAKE_CASE` |
| Files/modules | `snake_case.py` | `kebab-case.sh` or `snake_case.sh` |

---

## SOLID Principles
1. **SRP** – Each module/function has one reason to change.
2. **OCP** – Open for extension, closed for modification.
3. **LSP** – Subtypes must be substitutable for their base types.
4. **ISP** – Don't force clients to depend on methods they don't use.
5. **DIP** – Depend on abstractions, not concretions.

---

## Code Quality Rules

- **Small functions** – Do one thing; if it needs a comment to explain what it does, extract it.
- **DRY** – Extract repeated logic; never copy-paste blocks.
- **Fail fast** – Validate inputs at boundaries; raise explicit errors early.
- **Explicit error handling** – Never swallow exceptions; use typed exceptions in Python.
- **No magic numbers/strings** – Name all constants.
- **Structured logging** – Use `logging` (Python) or `logger` functions (Bash); never raw `print`/`echo` for diagnostics.
- **Config externalization** – `.env`, `pyproject.toml`, or config files; never hardcode secrets or paths.
- **Immutability by default** – Prefer `tuple`/`frozenset` in Python; `readonly` in Bash where practical.

---

## Code Smells to Avoid

- **Bloaters** – Long functions (>20 lines), large classes, >3 function parameters.
- **Duplicated code** – Apply extract-function/method immediately.
- **God object** – One class doing everything; split by responsibility.
- **Feature envy** – Method uses another class's data more than its own; move it.
- **Primitive obsession** – Wrap domain primitives in named types or dataclasses.
- **Deep nesting** – Invert conditions and return early instead.
- **Message chains** – `a.b().c().d()` — use a facade or intermediate variable.
- **Inconsistent naming** – Ambiguous or misleading identifiers.

---

## Agentic Workflow

Every non-trivial task follows this sequence. Do not skip steps.

1. **Plan** – Use `todowrite` to break the task into discrete steps before touching any file. For heavy multi-phase work, activate the `/deepwork` skill.
2. **Read first** – Read all relevant files before proposing changes. Never modify code you haven't read. Delegate broad discovery to `@explorer`.
3. **Execute** – Edit files using `edit` (prefer over `write` for existing files). One responsibility per change. Delegate bounded implementation to `@fixer`; delegate UI/UX work to `@designer`.
4. **Test** – Run existing tests. Add tests for new behaviour. Tests must pass before proceeding.
5. **Verify** – For 3+ file edits or any backend/API change, delegate review to `@oracle` via the `task` tool. Only proceed on `PASS`.
6. **Simplify** – Run the `/simplify` skill on changed code to catch smells and redundancy.
7. **Commit** – Small atomic commits using Conventional Commits format (see below).
8. **Document** – Update docstrings, README, and config comments affected by the change.

---

## Commit Message Format (Conventional Commits)

```
<type>(<scope>): <short summary>

[optional body: what changed and why]
[optional footer: breaking changes, issue refs]
```

Types: `feat`, `fix`, `refactor`, `test`, `docs`, `chore`, `ci`

Examples:
```
feat(auth): add token refresh on 401 response
fix(parser): handle empty input without raising KeyError
refactor(utils): extract validate_path into separate module
```

---

## File Structure

### Python Projects
```
<project>/
├── src/
│   ├── core/          # Business logic — no I/O, no side effects
│   ├── services/      # External interactions (APIs, DB, filesystem)
│   ├── models/        # Dataclasses, Pydantic models, DTOs
│   ├── utils/         # Pure utility functions
│   ├── handlers/      # Entry points (CLI args, HTTP handlers)
│   └── config/        # Config loaders and env parsing
├── tests/
│   ├── unit/
│   ├── integration/
│   └── e2e/
├── .github/workflows/ # CI pipelines
├── pyproject.toml     # Build system, deps, tool config (ruff, mypy, pytest)
├── .env.example       # Document required env vars; never commit .env
└── README.md
```

### Bash Projects
```
<project>/
├── bin/               # Executable entry-point scripts
├── lib/               # Sourced library functions (one concern per file)
├── config/            # Config files sourced at runtime
├── tests/             # bats tests (bash automated testing system)
├── .env.example
└── README.md
```

---

## Python-Specific Practices

- **Virtual environments** – Always use `venv` or `uv`; never install globally.
- **Dependency management** – `pyproject.toml` as the single source of truth; pin versions in `requirements.lock` if needed.
- **Type hints** – Add to all public functions; run `mypy --strict` in CI.
- **Linting/formatting** – `ruff check` + `ruff format` (replaces flake8/black/isort).
- **Testing** – `pytest` with `pytest-cov`; aim for >80% coverage on `src/core/` and `src/services/`.
- **Docstrings** – Google style for all public functions, classes, and modules.
- **Dataclasses/Pydantic** – Prefer over raw dicts for structured data.

## Bash-Specific Practices

- **Shebang** – Always `#!/usr/bin/env bash`.
- **Strict mode** – Every script starts with `set -euo pipefail`.
- **Shellcheck** – All scripts must pass `shellcheck` with no warnings.
- **Functions over inline code** – Extract any logic >5 lines into a named function in `lib/`.
- **Quoting** – Always double-quote variables: `"$var"`, `"${array[@]}"`.
- **Error messages** – Write to stderr: `echo "error: ..." >&2`.
- **Exit codes** – Use meaningful exit codes; document them in the script header.
- **No global state mutations** – Functions should not modify caller variables without `local`.

---

## Development Practices

- **Git** – Feature-branch workflow; squash-merge PRs; no force-push to `main`.
- **Pre-commit** – `ruff`, `mypy`, `shellcheck`, `pytest` (fast unit tests only).
- **CI** – Full test suite on every PR; block merge on failures; enforce coverage.
- **Security** – No secrets in code or git history; use `detect-secrets` in pre-commit.
- **Observability** – Structured logs with level, timestamp, and context; no bare `print`.

---

## opencode Tool Usage

| Task | Tool / Skill |
|---|---|
| Plan and track multi-step work | `todowrite` |
| Search for files | `glob` (never `find` via bash) |
| Search file contents | `grep` (never `grep` via bash) |
| Read a file | `read` (never `cat` via bash) |
| Edit an existing file | `edit` (prefer over `write`) |
| Create a new file | `write` |
| Run shell commands | `bash` |
| Delegate to a specialist | `task` (see OMO-slim specialists below) |
| Clean up changed code | `/simplify` skill |
| Heavy multi-phase sessions | `/deepwork` skill |
| Risky or parallel work | `/worktrees` skill |
| Learn from past sessions | `/reflect` skill |
| Map an unfamiliar codebase | `/codemap` skill |
| Inspect dependency internals | `/clonedeps` skill |

**Parallel tool calls**: make all independent tool calls in the same response block to maximise efficiency.

**Verification gate**: any task touching 3+ files or changing an API/backend MUST be reviewed by `@oracle` (via `task` tool) before reporting completion to the user.

---

## oh-my-opencode-slim Specialists

oh-my-opencode-slim is installed and provides specialist subagents. Delegate to them via the `task` tool rather than doing all work inline.

| Agent | When to delegate |
|---|---|
| `@explorer` | Codebase discovery, file/symbol search, pattern matching |
| `@librarian` | External docs, library APIs, web research, version-specific behaviour |
| `@oracle` | Architecture decisions, complex debugging, code review, simplification strategy |
| `@designer` | UI/UX work, responsive layouts, visual polish, component design |
| `@fixer` | Bounded mechanical implementation across multiple files |

**Delegation rules:**
- Delegate discovery to `@explorer` before planning — it is 2× faster and half the cost.
- Delegate library/API questions to `@librarian` rather than guessing.
- Delegate code review and architectural risk to `@oracle` — do not skip this for significant changes.
- Delegate all user-visible UI work to `@designer`; preserve designer output exactly in follow-up phases.
- Delegate scoped, well-defined implementation to `@fixer`; split large changes into parallel bounded lanes.
- Never duplicate work already delegated to a running background agent.

---

## Operational Best Practices

### Background Task Discipline

When delegating to specialists, use `background: true` for independent work:

- Launch independent discovery, research, or implementation tasks in the background so the main thread stays unblocked.
- Do not poll running background tasks or try to consume their output early — wait for the hook-driven completion notification.
- If no independent work remains while background jobs run, stop and let the completion event resume the workflow.
- Track each delegated task's agent, purpose, and file ownership to avoid conflicts.

### Specialist Session Reuse

When delegating to the same specialist repeatedly, reuse its session to preserve context and reduce cost:

- Pass the previous `task_id` (or Background Job Board alias) to `task()` instead of starting a fresh session.
- When a specialist already has context from prior work, reuse rather than re-explaining.
- Prefer the most recently used matching session when multiple options exist.

### LSP-Based Validation

After editing code, use the language server to catch errors before running tests:

```python
# After editing a Python file, check for type errors and diagnostics
lsp_diagnostics -> fix any found -> lsp_format_document if needed
```

- Run `lsp_diagnostics` on edited files to catch type errors, missing imports, and warnings.
- Use `lsp_format_document` to keep code formatted consistently.
- For project-wide checks, use `lsp_workspace_diagnostics` after opening the relevant files.

### Shell-Based Test Execution

Run tests via the `bash` tool, not inside editors or inlined:

```bash
# Python
pytest tests/ -x --tb=short

# Linting
ruff check src/ && ruff format --check src/
mypy src/

# Bash
shellcheck bin/*.sh lib/*.sh
bats tests/
```

- Run the full test suite (or the relevant test subset) after every implementation phase.
- Let the exit code and output determine pass/fail — do not skip actual test execution.

### Config Changes Require Restart

opencode loads all configuration at startup and does not hot-reload:

- Changes to `opencode.json`, `oh-my-opencode-slim.json`, agent prompt files, skills, or MCP configs take effect **on the next opencode run**.
- After editing any config file, restart opencode for the changes to apply.
- The running session keeps using the already-loaded configuration until then.

### Error Recovery (Config Escape Hatches)

If a config change breaks opencode's startup, use these environment variables to recover:

```bash
# Skip the project's local opencode.json and start from globals only
OPENCODE_DISABLE_PROJECT_CONFIG=1 opencode

# Load an explicit alternate config
OPENCODE_CONFIG=/path/to/backup.json opencode

# Skip external plugins
OPENCODE_PURE=1 opencode
```

This lets you edit and fix the broken config from inside opencode.
