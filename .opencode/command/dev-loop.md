---
description: Canonical two-track development process — PLAN (grill) → BUILD → GATE. Use when starting any non-trivial task before writing code or making changes.
---

# Dev Loop

One loop, two tracks, three stages: **PLAN → BUILD → GATE.**

## Step 0: Pick the track

Classify the task before doing anything:

- **CODE track** — feature, bugfix, refactor, or any change to TypeScript/program logic that can be tested. Domain logic, API endpoints, validation flows, parsers, Effect services.
- **CHANGE track** — config edits, ops/infra changes, deployment, docs, skill/command/agent edits. Not TDD-shaped; forcing tests here is theater.

If genuinely mixed, split it: CODE part in CODE track, CHANGE part in CHANGE track.

## Stage 1: PLAN (both tracks) — grill first

Before any edit, stress-test the plan using the **grill-me** discipline (`/grill-me`):

- Interview relentlessly, **one question at a time**, walking down each branch of the decision tree.
- For each question, **provide your recommended answer.**
- If a question can be answered by exploring the codebase, **explore instead of asking.**
- Resolve dependencies between decisions before moving on.

Stop grilling when the plan has no unresolved forks. Do not start work with open questions.

## Stage 2: BUILD

### CODE track — vertical TDD (see `/tdd` for full mechanics)

- **Vertical slices, never horizontal.** One `RED → GREEN → REFACTOR` cycle at a time.
- **Integration tests through public interfaces.** Test *what* the system does, not *how*. Mock only at system boundaries (external APIs, DB, filesystem, time) — never your own collaborators.
- **Behavior over implementation.** A test that breaks on a rename was testing the wrong thing.
- **30-second interface plan** before writing: interface shape, which behaviors matter most, target module.
- Run tests with `bun test` from the package dir (e.g. `packages/opencode`). Never from repo root.
- Run `bun typecheck` to verify types before gating.

### CHANGE track — verify by observation

- Make the change.
- **Verify by direct observation, never assumption.** An unobserved result (empty output, exit 0 with no body, "should work") is *not* a pass — observe the actual artifact.
- State the evidence inline: what you ran, what you saw.

## Stage 3: GATE

Route the result through **no-mistakes** so nothing reaches the real remote unreviewed:

- `git push no-mistakes <branch>` runs review → test → docs → lint in a disposable worktree and opens a clean PR only when every check is green.
- Act on findings: approve auto-fixes, decide the escalated ones.

**Graceful degradation:** if `no-mistakes` is not installed, fall back to small logical commits with descriptive messages + a manual PR. Never batch a large pile of work into one commit.

## Why this exists

Effort that never becomes reviewed, committed history is lost work. This loop makes "planned, built in the right track, gated into a clean PR" the path of least resistance.
