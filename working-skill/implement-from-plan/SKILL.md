---
name: implement-from-plan
description: Implement code from an existing plan document and run repeated review-fix passes until the change is clean. Use when the user provides or references a plan, spec, roadmap, design doc, task list, `plan.md`, 설계 문서, or 작업 계획서 and expects execution plus disciplined post-implementation review for plan fidelity, bugs, integration, side effects, refactoring, dead-code cleanup, and final quality gating. Trigger on requests such as "계획 문서대로 구현", "plan 기준으로 작업", or "findings가 없어질 때까지 계속 수정".
---

# Implement From Plan

Execute the plan instead of redesigning it. Keep the implementation aligned to the plan, then keep reviewing and fixing until the last pass is clean.

## Intake

- Locate the plan document first.
- Identify the target workspace or repository.
- Capture any explicit constraints such as compatibility requirements, migration limits, test boundaries, or files that must not change.
- Search the workspace for likely filenames such as `plan`, `spec`, `design`, `roadmap`, `tasks`, or `todo` if the plan path is not given.
- Escalate instead of guessing when the plan is ambiguous, stale, or materially conflicts with the codebase.

## Workflow

1. Ground the task in the plan.
   - Read the plan before touching code.
   - Extract deliverables, constraints, sequencing, dependencies, and acceptance criteria.
   - Compare the plan against the current codebase and note only material contradictions.
   - Identify the most relevant local verification commands early instead of assuming a preferred test runner.
   - Convert the plan into a short execution checklist using the normal planning mechanism available in the environment.

2. Implement in plan order.
   - Start with the smallest vertical slice that meaningfully advances the plan.
   - Reuse existing modules, patterns, utilities, and abstractions before creating new ones.
   - Add or update tests alongside behavior changes when the plan or risk profile warrants it.
   - Keep edits incremental and verify the narrowest affected surface after each meaningful change.

3. Reach a first working state.
   - Do not begin review passes while the implementation is obviously incomplete.
   - Once the plan is implemented well enough to execute or inspect coherently, begin the mandatory review loop.

4. Run the mandatory review-fix loop.
   - Use the ordered passes in `references/review-checklist.md`.
   - Treat each pass as a search for actionable findings tied to correctness, maintainability, integration, or scope.
   - Fix material findings immediately when they can be resolved safely within scope.
   - Re-run the narrowest useful verification after each fix.

5. Repeat until clean.
   - Continue cycling through the review passes until no new actionable findings remain.
   - If a later fix changes architecture, control flow, shared state, public contracts, persistence behavior, or file boundaries, restart from the earliest affected pass.
   - Do not stop after a single clean-looking pass if there are unreviewed categories left.

## Operating Rules

- Prefer concrete findings over generic commentary.
- Fix issues instead of merely listing them when the fix is safe and within scope.
- Ignore trivial stylistic nits unless they block readability or violate clear project conventions.
- Preserve unrelated user changes and local codebase conventions.
- Favor smaller, composable edits over broad rewrites unless the plan explicitly calls for large restructuring.
- When the plan and the codebase conflict in a way that could cause unsafe work or major rework, pause and ask for clarification.
- Keep the scope anchored to the plan unless extra changes are required to preserve correctness or integration.

## Communication

- Keep progress updates short, concrete, and tied to the current execution or review pass.
- Explain findings in terms of user-visible risk, correctness, maintainability, or scope impact.
- Finish by summarizing implemented scope, verification that ran, checks that were skipped, and any residual risk.

## Verification

- Run tests, builds, linters, or targeted checks that match the touched area.
- Prefer the repository's native verification flow. If a common tool is unavailable, use an equivalent local command that already fits the project instead of installing unrelated tooling by default.
- Prefer narrow checks while iterating and at least one broader confidence check before finishing when practical.
- Add or update tests for changed behavior and for edge cases fixed during review when automated coverage is available.
- Verify explicit acceptance criteria from the plan instead of assuming they are covered by code inspection.
- When no automation exists, perform manual review of affected paths and explain the reasoning that supports the change.

## Reference

Read `references/review-checklist.md` before the first review pass and revisit it after any substantial fix.
