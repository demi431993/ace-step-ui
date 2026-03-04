# Codebase Issue Sweep: Proposed Tasks

## 1) Typo fix task
**Task:** Fix the typo `python_embeded` in user-facing docs/comments where it is not a literal filesystem path requirement, and add a short note where the misspelling is intentionally required by upstream package layout.

- **Why:** `python_embeded` is a misspelling of `python_embedded`, which is easy to misread and can confuse contributors.
- **Evidence:** The term appears in prose/comments, e.g. README and service comments. In places where it is an actual path probe, we should keep behavior and clarify intent.
- **Acceptance criteria:**
  - Non-path prose/comment text is corrected to `python_embedded`.
  - Any real path usage remains functional.
  - A brief explanatory note is added near path checks to avoid accidental regressions.

## 2) Bug fix task
**Task:** Make startup scripts actually honor configurable frontend port values instead of only printing them.

- **Why:** `start.sh` and `start-all.sh` print/use `FRONTEND_PORT` in messages and URLs, but they start Vite with plain `npm run dev` (without `-- --port ...`), so the configured value is ignored.
- **Evidence:** Scripts announce `http://localhost:${FRONTEND_PORT:-3000}` but launch frontend without forwarding the port flag.
- **Acceptance criteria:**
  - `start.sh` and `start-all.sh` pass `--port "${FRONTEND_PORT:-3000}"` to Vite.
  - Browser auto-open URL uses the same resolved port.
  - Quick smoke run confirms frontend binds to the configured port.

## 3) Code comment / documentation discrepancy task
**Task:** Align the Gradio positional argument comments with the actual payload semantics in `buildGradioArgs`.

- **Why:** Some inline comments are ambiguous/mismatched (e.g., singular/plural naming for indices 43/44), which increases risk when maintaining a fragile positional API.
- **Evidence:** `params.completeTrackClasses` is commented as `Track Names`, while index 43 is `Track Name`; the naming suggests a class list and can be clarified.
- **Acceptance criteria:**
  - Each positional argument comment accurately matches the corresponding field meaning.
  - Terminology is consistent (`track name` vs `track classes`).
  - Optional follow-up: extract these mappings to a typed constant map to reduce drift.

## 4) Test improvement task
**Task:** Convert `server/src/scripts/test-queue.ts` into an automated test with deterministic assertions and CI integration.

- **Why:** Current queue validation is an ad-hoc script that prints an order but does not fail on ordering regressions via explicit assertions.
- **Evidence:** The script logs `[test-queue] OK` regardless of stricter fairness expectations and is not wired into a test runner.
- **Acceptance criteria:**
  - Add a real test (e.g., Vitest/Jest) for queue scheduling behavior.
  - Assert expected concurrency/tier behavior and completion ordering invariants.
  - Add test command to server `package.json` and run it in CI.
