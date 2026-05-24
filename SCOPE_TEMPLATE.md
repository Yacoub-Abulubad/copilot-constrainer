# Branch Scope: `<branch-name>`

**Created:** YYYY-MM-DD
**Author:** `<your-name>`
**Issue / Ticket:** #`<number>` _(optional — link to GitHub issue or external tracker)_
**Target Branch:** `develop` / `main`

---

## 🎯 Goal

> **One or two sentences only.** What problem does this branch solve, and for whom?
> Bad: "Improve the rendering pipeline."
> Good: "Add feather-blending support to MaskCompositor so that edited regions fade naturally into the original image."

## ✅ In Scope

> Be specific. Vague items invite scope creep.

- [ ] Item 1
- [ ] Item 2
- [ ] Item 3

## 🚫 Out of Scope

> Explicitly name things you are NOT doing. This is as important as In Scope.
> Future work belongs in a new branch — not here.

- NOT refactoring `<module>` (separate concern)
- NOT fixing `<other bug>` (will open a separate issue)
- NOT adding `<feature>` (out of priority for this cycle)

## 📐 Acceptance Criteria

> Each criterion should be **measurable and testable**. If you can't test it, rewrite it.

- [ ] `pytest tests/ -v` passes with no regressions
- [ ] Criterion specific to this feature
- [ ] Criterion specific to this feature

## 🔗 Dependencies

> List PRs, branches, or external work that must land before this branch can merge.

- None _(or list them)_

## 📝 Agent Notes

> Context useful to a Copilot agent picking up this work mid-session.
> Include: key files to touch, approach decisions already made, things to avoid.

- Key files: `src/...`
- Approach: ...
- Avoid: ...
