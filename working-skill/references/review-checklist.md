# Review Checklist

## Execution Harness

Run every review pass inside this harness.

- Fixed harness: approved plan, explicit constraints, repository conventions, selected verification commands, current red/green proof for the active slice
- Mutable surface: smallest in-scope tests, production files, and fixtures for the current slice or fix
- Keep: clearer requirement proof, higher plan fidelity, fewer findings, simpler green change set
- Discard: tests that never went red for the intended reason, behavior changes without new proof, failed checks, scope creep, duplicate complexity, speculative changes with no clear gain
- Stop: acceptance criteria met, relevant verification passes, final pass produces no new actionable findings

Use these passes after the first working implementation and after any substantial fix. Treat numeric size thresholds as heuristics, not hard rules.

## 0. TDD fidelity review

- Verify that each new or changed requirement is tied to a proving automated test when practical, or to an explicit manual fallback only after a light repo-native harness was ruled out.
- Confirm the active slice started red for the expected reason before production code changed.
- Confirm the green step used the smallest production change that satisfied the failing test.
- Confirm refactors happened only while the relevant suite stayed green.
- Flag any behavior-changing code path that lacks regression protection.

## 1. Plan fidelity review

- Verify that every required item in the plan is implemented.
- Verify that acceptance criteria map to tests or explicit manual checks instead of relying on inspection alone.
- Remove behavior, scope, or files that the plan did not ask for unless they are required to preserve correctness or integration.
- Check names, data flow, UI states, error handling, and edge cases against the plan.
- Validate every explicit acceptance criterion.
- Remove or rewrite speculative changes that do not improve plan fidelity relative to the current best-known state.

## 2. Bug and critical issue review

- Look for correctness bugs, crash paths, invalid state transitions, race conditions, corruption, or data-loss risks.
- Exercise null, empty, timeout, retry, rollback, and failure paths.
- Check for security, privacy, or permission regressions.
- When a new bug is found, reproduce it with the smallest failing test before fixing it when practical.
- Verify callers, migrations, and compatibility boundaries.

## 3. Large function and file review

- Flag functions that are hard to understand in one read, mix abstraction levels, or sprawl past roughly 80-120 lines.
- Flag files that span multiple responsibilities or grow past roughly 300-500 lines without a clear single purpose.
- Extract focused helpers, components, or modules only when the new boundary improves clarity and stays covered by green tests.
- Avoid splitting code into thin wrappers that hide control flow.

## 4. Integration and reuse review

- Reuse an existing utility, abstraction, component, hook, or service when it already solves the problem cleanly.
- Respect existing dependency direction, naming, ownership, and layering rules.
- Merge safe duplication into a shared path when it improves consistency without over-generalizing.

## 5. Side-effect review

- Check whether the change altered unrelated behavior, shared state, persistence semantics, caching, logging, metrics, or performance.
- Verify that observers, lifecycle hooks, async tasks, retries, and background jobs fire only as intended.
- Check for accidental public contract changes or user-visible behavior shifts outside scope.

## 6. Whole-change review

- Read the entire change set end-to-end as a reviewer, not just the last edited function.
- Check consistency across implementation, tests, configuration, migrations, and cleanup.
- Reconcile assumptions that drifted across separate edits.
- Confirm that no abandoned experiment or half-kept workaround remains in the final diff.

## 7. Dead code cleanup review

- Remove unused helpers, stale branches, obsolete types, temporary adapters, and duplicate tests.
- Remove transitional scaffolding that no longer serves the final solution.
- Keep compatibility code only when it is still required and easy to identify later.
- Remove artifacts of discarded implementation attempts when they are still visible in code or tests.

## 8. Quality review

- Confirm that the code is understandable without tracing too many unrelated files.
- Protect boundaries and invariants, and make failure modes readable.
- Match test coverage and assertion precision to the risk of the change.
- Leave a structure that another engineer can extend or debug later.

## 9. Final zero-finding review

- Run one last pass over the full change set.
- Fix any new actionable finding and revisit every earlier pass affected by that fix.
- Stop only when the final pass produces no additional material findings.
- Confirm that every kept behavior-changing edit has evidence: red-to-green proof, plan alignment, verification, or a clearly documented fallback rationale.

## Escalate Instead Of Guessing

- Escalate when the plan is missing critical implementation details that would likely cause rework.
- Escalate when the plan conflicts with the codebase in a way that affects architecture, safety, or compatibility.
- Escalate when required validation cannot be run locally.
- Escalate when safe TDD would require an automated harness that cannot be added within scope.
