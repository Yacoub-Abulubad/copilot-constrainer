# Copilot Constrainer

A framework for keeping GitHub Copilot focused and preventing scope creep during AI-assisted development.

## The Problem

AI coding agents are eager to help — sometimes *too* eager. They'll refactor unrelated code, add features you didn't ask for, and "improve" things outside your current task. This scope creep wastes time, introduces risk, and makes PRs harder to review.

## The Solution

This repo provides a **scope-first protocol** that forces Copilot to:

1. **Check** for a scope file before writing any code
2. **Refuse** work that falls outside the defined scope
3. **Redirect** out-of-scope requests to new branches

The result: focused, predictable, reviewable AI-assisted development.

## How It Works

### 1. Branch Scope Files

Every branch gets a `<branch-name>_SCOPE.md` file in the repo root that defines:

- **Goal** — What this branch accomplishes (1–2 sentences)
- **In Scope** — Specific changes to make
- **Out of Scope** — What is explicitly *not* being done
- **Acceptance Criteria** — How to know it's done

See [SCOPE_TEMPLATE.md](SCOPE_TEMPLATE.md) for the full template.

### 2. Copilot Instructions

The [`.github/copilot-instructions.md`](.github/copilot-instructions.md) file is loaded automatically by GitHub Copilot in every session. It enforces:

- **Hard stops** when requests fall outside scope
- **Scope file creation** when no scope file exists for a branch
- **Push-back** on vague requirements before any work begins

### 3. Automatic Archival

When a PR merges, the GitHub Actions workflow ([`.github/workflows/archive-scope-on-pr.yml`](.github/workflows/archive-scope-on-pr.yml)) automatically moves the scope file to `Archived_Branch_Scopes/` with a date prefix. These archived files serve as lightweight ADRs (Architecture Decision Records).

## Quick Start

1. **Copy the framework into your repo:**
   - `.github/copilot-instructions.md` — the agent instructions
   - `.github/workflows/archive-scope-on-pr.yml` — the archival workflow
   - `SCOPE_TEMPLATE.md` — the scope file template
   - `Archived_Branch_Scopes/` — directory for merged scope files

2. **Adapt the instructions** to your project's architecture (replace the project-specific sections in `copilot-instructions.md`).

3. **Start a new branch** and create a scope file before asking Copilot to implement anything.

## What Copilot Does Differently

| Without scope file | With scope file |
|---|---|
| Implements whatever you ask | Checks scope first |
| Sneaks in refactors | Only touches what's defined |
| Answers vague requests | Pushes back for clarity |
| Unbounded changes | Hard stops at scope boundaries |

## Example Interaction

```
You: "Can you also refactor the auth module while you're at it?"

Copilot: ⛔ Stopped. This is out of scope for `feature/add-login`.
The scope file (feature_add-login_SCOPE.md) does not cover this request.

What to do instead:
1. Create a new branch: git checkout -b refactor/auth-module
2. Scaffold a scope file: bash scripts/create-scope.sh
3. Fill in the scope, commit it, then start a new Copilot session.
```

## Repository Structure

```
.github/
├── copilot-instructions.md        # Copilot agent instructions (scope protocol)
└── workflows/
    └── archive-scope-on-pr.yml    # Auto-archives scope files on PR merge
Archived_Branch_Scopes/            # Historical scope files from merged branches
SCOPE_TEMPLATE.md                  # Template for new scope files
```

## License

[MIT](LICENSE)
