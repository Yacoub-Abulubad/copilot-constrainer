# GitHub Copilot Agent Instructions — Archi-Rendering

These instructions are loaded automatically by GitHub Copilot in every session.
Follow them strictly before writing any code.

---

## 🔒 Scope-First Protocol (MANDATORY — Do This Before Anything Else)

### Step 1: Identify the current branch

```bash
git rev-parse --abbrev-ref HEAD
```

Sanitize the branch name for use as a filename: replace every `/` with `_`.

> Example: `feature/add-login` → scope file is `feature_add-login_SCOPE.md`

### Step 2: Find the scope file

Look for `<sanitized-branch-name>_SCOPE.md` in the **repository root**.

---

#### ✅ If the scope file EXISTS

1. Read it in full before doing anything else.
2. Internalize the goals, constraints, and **Out of Scope** items.
3. Reference it when making every implementation decision.
4. If the user asks for something **outside the defined scope**, issue a **hard stop**:

   > ⛔ **Stopped. This is out of scope for `<branch-name>`.**
   > The scope file (`<scope-file>`) does not cover this request.
   >
   > **What to do instead:**
   > 1. Create a new branch for this work: `git checkout -b <appropriate-branch-name>`
   > 2. Scaffold a scope file: `bash scripts/create-scope.sh`
   > 3. Fill in the scope, commit it, then start a new Copilot session on that branch.

   **Do NOT:**
   - Ask the user follow-up questions about what they want to do with the out-of-scope request.
   - Offer to update the scope file on their behalf.
   - Partially implement anything related to the request.
   - Suggest alternatives that still touch out-of-scope areas.

   Simply stop, state what is out of scope and why, and redirect to a new branch.

5. Do not "sneak in" related improvements, refactors, or bug fixes that aren't in scope.
   If you spot something worth fixing, note it: *"Out-of-scope issue noticed: [description]. Logged for a future branch."*

---

#### ❌ If the scope file DOES NOT EXIST

Do **not** begin any implementation work.

Ask the user:
> "No scope file found for this branch. Let's define the scope before we start — this keeps work focused and prevents drift.
> 
> Please answer the following:
> 1. **Goal:** What exactly does this branch need to accomplish? (1–2 sentences)
> 2. **In scope:** What specific things will be changed or added?
> 3. **Out of scope:** What are you explicitly *not* doing on this branch?
> 4. **Acceptance criteria:** How will you know it's done?
> 5. **Issue/ticket number:** (optional, for traceability)
>
> I'll create the scope file once you answer these."

Push back on vague answers. Examples:
- *"Add some improvements to the API"* → Ask: "Which endpoint? What kind of improvements — performance, new fields, error handling?"
- *"Clean things up"* → Ask: "Which files? What does 'clean' mean here — formatting, removing dead code, restructuring?"
- *"Fix the bug"* → Ask: "Which bug? Can you describe the current behaviour vs expected behaviour?"

Once the user provides answers, create the scope file using the template in `SCOPE_TEMPLATE.md`.
Run `bash scripts/create-scope.sh` to scaffold it, then fill in the details.
Ask the user to commit it before you proceed.

---

## 🏗️ Project Architecture (Archi-Rendering)

An AI-powered architectural rendering service (FLUX diffusion + SAM2 segmentation).

| Layer | Directory | Rule |
|---|---|---|
| Domain logic | `src/core/` | No external deps except PIL, numpy |
| Model wrappers | `src/models/` | Thin wrappers, handle model lifecycle |
| Business logic | `src/services/` | Orchestrate core + models |
| API layer | `src/api/` | FastAPI routes, schemas, HTTP handling |
| Dev/test tools | `dev/` | **Never** imported in production code |
| Production entry | `prod/` | Ray Serve deployment only |
| Tests | `tests/` | pytest, run before and after changes |

See `DEVELOPMENT.md` for full conventions, patterns, and examples.

---

## ✅ General Agent Rules

### Code changes
- Make **surgical, minimal changes** — only what scope defines.
- Prefer editing existing files over creating new ones.
- Do not refactor unrelated code, even if it looks messy.
- Always run existing tests before and after: `pytest tests/ -v`
- Ask before adding new dependencies (pip or otherwise).

### Git discipline
- Use **conventional commits**: `feat:`, `fix:`, `refactor:`, `test:`, `docs:`, `chore:`
- Do not commit automatically generated code without review.
- If you generate code, **summarize the changes** in a comment for the user to review before committing.

### Communication
- When uncertain about scope, **ask before doing**.
- Surface trade-offs and alternatives; let the developer decide.
- Flag technical debt spotted outside scope — don't silently fix it.
- Summarise what you changed and why at the end of each task.

---

## 📦 Scope File Lifecycle

- Scope files live in the **repo root** during active branch work: `<branch>_SCOPE.md`
- When a PR merges to `develop` or `main`, the GitHub Actions workflow
  `.github/workflows/archive-scope-on-pr.yml` automatically moves the file to
  `Archived_Branch_Scope/` for historical reference.
- Archived scope files are a useful record of *why* things were built — treat them as lightweight ADRs.
