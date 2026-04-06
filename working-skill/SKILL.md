---
name: working-skill
description: Execute an existing plan with tests-first TDD and bounded keep/discard loops until the change is clean. Use when the user provides or references a plan, spec, roadmap, design doc, task list, `plan.md`, 설계 문서, or 작업 계획서 and expects plan-first execution, red-green-refactor slices, small verified edits, repeated review-fix passes, and final zero-finding quality gating. Trigger on requests such as "계획 문서대로 구현", "plan 기준으로 작업", "TDD로 구현", or "findings가 없어질 때까지 계속 수정".
---

# working-skill

Execute the plan via tests-first TDD instead of redesigning it. Run implementation like a bounded `autoresearch` loop: keep the approved plan and verification harness fixed, encode the next requirement as the smallest useful failing test, make the minimum in-scope code change to pass it, refactor while tests stay green, and keep the result only if it improves the current best-known state.

## Operating Model

- 고정 하네스: 승인된 계획 문서, 명시적 제약, 저장소 규칙, 확인한 검증 명령, 활성 구현 슬라이스의 실패/성공 테스트 근거, 사용자 지시
- 변경 가능한 산출물: 현재 구현 슬라이스에 필요한 최소 프로덕션 파일, 테스트, 픽스처
- Keep 기준: 계획 충실도 증가, 실패 테스트가 의도한 이유로 시작해 통과로 전환됨, actionable finding 감소, green 상태를 유지한 단순화
- Discard 기준: 의도한 이유로 실패하지 않는 테스트, 실패 테스트 없이 들어간 동작 변경, 검증 실패, scope creep, 중복/임시 복잡도 증가, 계획 대비 근거 없는 우회 구현
- 종료 조건: acceptance criteria 충족, 가능한 범위의 요구사항이 테스트 또는 명시적 수동 검증으로 입증됨, 필요한 검증 통과, `references/review-checklist.md`의 final zero-finding pass 종료

자동으로 끝없이 돌지 말고, 위 종료 조건을 만족하면 멈춘다.

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
   - Identify the most relevant local verification commands early instead of assuming a preferred test runner.
   - Compare the plan against the current codebase and note only material contradictions.
   - Establish the initial best-known-good state: current git state, relevant baseline behavior, and the smallest first implementation slice.
   - Break the plan into ordered behavior slices and map each slice to a proof target: preferred failing automated test, otherwise the lightest repo-native fallback that can still verify the requirement.
   - Mark which acceptance criteria already have coverage and which must start with a new failing test.
   - Convert the plan into a short execution checklist using the normal planning mechanism available in the environment.

2. Run a bounded red-green-refactor loop.
   - Start with the smallest vertical slice that meaningfully advances the plan.
   - Reuse existing modules, patterns, utilities, and abstractions before creating new ones.
   - Red: write or update the smallest automated test that proves the next acceptance criterion, then run it and confirm it fails for the expected reason before touching production code.
   - If no automated check exists, add the lightest viable repo-native harness first when practical. Only fall back to explicit manual verification when automation is genuinely unavailable or unsafe to add within scope.
   - Green: edit only the smallest necessary production surface to make the current red test pass. Do not mix in speculative cleanup or unrelated behavior changes.
   - Refactor: once the active slice is green, improve names, duplication, structure, and test readability while keeping the relevant suite green after each meaningful cleanup.
   - Verify the narrowest affected surface after each meaningful red, green, or refactor step.
   - Keep the change only if it improves plan fidelity or removes a concrete finding without introducing regression.
   - If a speculative idea fails, or the test failed for the wrong reason, discard only your latest idea by reverting or rewriting your own last change and return to the last verified green state. Never revert unrelated user edits.

3. Reach a first working state.
   - Do not begin review passes while the implementation is obviously incomplete.
   - Do not claim a slice is done until its proving test is green and any required integration behavior has been rechecked.
   - Once the plan is implemented well enough to execute or inspect coherently, begin the mandatory review loop.

4. Run the mandatory review-fix loop.
   - Use the ordered passes in `references/review-checklist.md`.
   - Treat each pass as a search for actionable findings tied to correctness, maintainability, integration, or scope.
   - For each material behavior bug or regression risk, add the smallest failing test that reproduces it before changing production code.
   - Fix one material finding at a time when it can be resolved safely within scope.
   - For structure-only findings, refactor under green tests instead of changing behavior speculatively.
   - Re-run the narrowest useful verification after each fix.
   - Keep review fixes that reduce findings or simplify the change set. Discard fixes that add complexity without improving the current best-known state.

5. Repeat until clean.
   - Continue cycling through the review passes until no new actionable findings remain.
   - If a later fix changes architecture, control flow, shared state, public contracts, persistence behavior, or file boundaries, add or adjust the proving test first and restart from the earliest affected pass.
   - Do not stop after a single clean-looking pass if there are unreviewed categories left.
   - Stop when the change meets the plan, the relevant checks pass, and the final zero-finding review adds nothing new.

## Operating Rules

- Prefer concrete findings over generic commentary.
- Fix issues instead of merely listing them when the fix is safe and within scope.
- Do not make behavior-changing production edits before capturing the intended behavior in a failing test unless automated verification is genuinely unavailable.
- Ignore trivial stylistic nits unless they block readability or violate clear project conventions.
- Preserve unrelated user changes and local codebase conventions.
- Favor smaller, composable edits over broad rewrites unless the plan explicitly calls for large restructuring.
- Prefer one meaningful red-green-refactor slice or one safe review fix at a time over stacked speculative edits.
- Use characterization tests before changing legacy behavior when the plan depends on current contracts that are not yet explicit.
- Keep track of the current best-known-good state and avoid piling new changes on top of unverified work.
- When the plan and the codebase conflict in a way that could cause unsafe work or major rework, pause and ask for clarification.
- Keep the scope anchored to the plan unless extra changes are required to preserve correctness or integration.
- If a change neither improves verification nor meaningfully advances the plan, remove or rewrite it instead of rationalizing it.

## Communication

- Keep progress updates short, concrete, and tied to the current plan, red, green, refactor, or review pass.
- Explain findings in terms of user-visible risk, correctness, maintainability, or scope impact.
- Call out the proof state when it matters: which test started red, what turned green, and what broader suite was re-run.
- Distinguish clearly between kept changes, discarded ideas, and residual risks when that history matters to the current state.
- Finish by summarizing implemented scope, verification that ran, checks that were skipped, and any residual risk.

## Verification

- Establish the most relevant baseline checks early so keep/discard decisions are evidence-based.
- Run tests, builds, linters, or targeted checks that match the touched area.
- Prefer the repository's native verification flow. If a common tool is unavailable, use an equivalent local command that already fits the project instead of installing unrelated tooling by default.
- Prefer narrow checks while iterating and at least one broader confidence check before finishing when practical.
- Start each behavior slice by running the new or updated test in a failing state, then rerun it in a passing state after the minimal production change.
- Add or update tests for changed behavior and for edge cases fixed during review when automated coverage is available.
- Verify explicit acceptance criteria from the plan with executable tests whenever practical instead of assuming they are covered by code inspection.
- When no automation exists and adding a light repo-native harness is not practical within scope, perform manual review of affected paths and explain the reasoning that supports the change.
- If a failed verification invalidates the current best-known state, roll back only the affected speculative change and resume from the last verified green state.

## Reference

Read `references/review-checklist.md` before the first review pass and revisit it after any substantial fix or any new failing test that changes the active proof boundary.
