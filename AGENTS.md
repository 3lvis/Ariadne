# AGENTS.md

This file is a reusable starting point for iOS repositories that want strong agent rules without locking themselves into one product domain.

It is designed to work for:

- Swift packages and app targets
- SwiftUI and UIKit apps
- local-first or reactive-state apps
- apps with device-specific behavior such as camera, sensors, AR, mapping, or media

Adapt the bracketed placeholders for your repo:

- `[LibraryPaths/**]`
- `[AppPaths/**]`
- `[BackendPaths/**]`
- `[BenchmarkCommand]`
- `[UIBuildCommand]`
- `[RegressionWorkflowName]`
- `[BugPlaybookPath]`

## Core Principles

- Prefer convention-first APIs and make boundary-specific behavior explicit.
- Keep read paths, write paths, persistence rules, and transport rules easy to distinguish.
- Treat local persistence or app state as the UI source of truth unless the project explicitly says otherwise.
- Keep architecture additive. New infrastructure should compose with platform-native patterns, not replace them casually.
- Be strict about payload semantics and document them in one place.

## Development Process

### Scoped TDD

- Strict TDD is required for behavior changes in foundational code such as:
  - `[LibraryPaths/**]`
  - `[BackendPaths/**]`
- For behavior additions or changes:
  - write or update tests first
  - run the relevant tests and confirm they fail for the expected reason
  - implement the change
  - rerun the relevant tests until they pass
- Removals are TDD-exempt:
  - remove the code first
  - run the relevant tests
  - remove or update tests that intentionally covered the deleted behavior

### App/UI Workflow

- Strict TDD is not always required for app-layer code in:
  - `[AppPaths/**]`
- Verify changes with relevant tests when available.
- For UI or behavior changes in `[AppPaths/**]`, run the app build before finishing the task.
- Manual QA can supplement verification, but it does not replace the required build step.

### Verification By Layer

- Core domain logic, serialization, persistence logic, geometry, routing, state reducers, and other deterministic code should be verified with focused automated tests.
- App UI flows should be verified with the strongest available combination of:
  - focused tests
  - app build
  - manual QA where necessary
- Device-dependent features such as camera, AR, location, motion, Bluetooth, audio, or sensors require device validation when the behavior depends on real hardware signals.

## Implementation Guidance

- Keep changes minimal and behavior-driven.
- Test contracts, not implementation details.
- Make diagnostics explicit and actionable when behavior cannot be resolved safely.
- Do not fix from instinct. Prove the failing layer first.
- If a new bug is discovered while working on a shared or integration branch, move that investigation onto a dedicated branch before debugging further.

### Performance Workflow

- Never optimize without a baseline.
- Before changing performance-sensitive code:
  - record a before-change baseline using the exact same benchmark or profiling command
  - implement the change
  - rerun the same measurement after the change
- Keep the benchmark path identical across before/after runs.
- Record enough context to make the numbers comparable:
  - device or simulator
  - dataset size
  - relevant feature flags
  - profiling mode

### Architecture Guardrail

Always ask:

- Can this app still feel like a normal platform app after adding this abstraction?

For iOS projects, that usually means:

- models still look like normal Swift or SwiftData models
- views still look like SwiftUI or UIKit, not framework-specific wrappers everywhere
- persistence still uses normal Apple framework concepts where possible
- infrastructure stays at the boundary where it adds real value

If a new abstraction starts pulling ordinary app behavior into its own framework-specific world, reconsider the API.

## Reactive Architecture Guidance

### Views React, Domain Layer Persists

- Treat views as reactive readers of local state.
- Views should:
  - render UI
  - collect user intent
  - read local or derived state
- Views should not own persistence, session orchestration, sync orchestration, or transport logic.

Put mutations and coordination into a domain or service layer.

- The domain layer performs mutations, persistence updates, syncs, or session changes.
- Views react to the resulting local state changes.

### Keep A Single UI Source Of Truth

- UI should refresh from local app state or persistence, not from transport responses stitched directly into view state.
- Backend responses, AR session updates, sensor events, and other inputs should update the source of truth.
- The UI should render from that source of truth, not from ad hoc side channels.

### Pass IDs And Scalars Across Boundaries

- Prefer passing identifiers and lightweight derived values across view and feature boundaries.
- Avoid passing heavy live model objects, session objects, or mutable framework-bound objects deeper than necessary.

This keeps:

- ownership clearer
- state fresher
- tests easier to write
- modal and navigation flows easier to reason about

### Save Or Submit Flow

Recommended pattern for create, edit, and confirm flows:

1. Parent screen presents the next UI with IDs or simple inputs.
2. The sheet, modal, or child flow owns temporary draft state.
3. The domain layer performs the mutation or submission.
4. The shared source of truth updates.
5. The parent and detail screens re-render from local state.

Practical rule:

- child flow submits intent
- domain layer performs the write
- screens react; they do not manually rebuild final UI state from transport responses

### State Machines

If a feature has meaningful modes, model them explicitly.

Examples:

- idle
- loading
- scanning
- placing anchors
- editing
- navigating
- rerouting
- failed

Prefer an explicit machine when:

- the feature has mode transitions
- async work changes available actions
- multiple inputs can race
- UI must enforce sequencing

## Bug-Solving Playbook

Use this workflow when a real bug exists and the right fix is not yet known.

### Principles

- Start from behavior, not theory.
- Locate the first wrong state.
- Prove the failing layer before changing behavior.
- Rebuild the cleanest fix from base once the cause is known.
- Keep the final branch cleaner than the investigation branch.

### Required Workflow

1. Reproduce the real bug in the actual user flow.
2. Define expected versus actual behavior.
3. Map the state path across layers.
4. Add diagnostics the failing run can actually surface.
5. Run one focused scenario at a time.
6. Isolate the narrowest plausible layer.
7. Separate product defects from test-surface defects.
8. Move bug work onto an investigation branch immediately.
9. Once the likely cause is known, branch cleanly from base.
10. Rebuild only the proven fix.
11. Ask whether the fix belongs deeper.
12. Keep or add tests that prove both behavior and mechanism where needed.
13. Remove temporary investigation residue.
14. Merge the clean, verified solution.

### First Wrong State Rule

Always map the state path from input to output.

Typical path:

- user action
- draft or transient state
- service or mutation layer
- persistence or local store
- reactive publisher or observer
- machine or view model
- rendered UI

For device-driven apps, the path may also include:

- sensor input
- session state
- tracking or world state
- coordinate transforms
- rendered overlays

The goal is to find the first wrong state, not just the final wrong symptom.

### Diagnostics Rules

- Add diagnostics before changing behavior.
- Diagnostics must be visible in the failing execution path.
- Use transports the failing run can actually surface:
  - assertion-attached trace output
  - test-readable artifacts
  - narrow test seams
  - targeted logs that the active run loop can observe

### Product Bug Versus Test-Surface Bug

A failing test does not always mean production behavior is wrong.

Two different failure classes exist:

- product bug: user-visible behavior is wrong
- test-surface bug: the test is reading an unstable or incidental surface

Do not change production behavior to satisfy a weak test surface unless that surface is part of the real product contract.

### Investigation Branch Discipline

Investigation creates residue:

- temporary logging
- probe assertions
- discarded fixes
- experimental tests

Do that work on an investigation branch.

Once the cause is known:

- return to the clean base branch
- create a fresh branch
- rebuild only the proven fix

## Spatial / AR / Mapping Guidance

Use this section for AR apps, mapping apps, robotics-style apps, or any feature with multiple coordinate spaces.

### Coordinate-Space Discipline

Always distinguish explicitly between:

- world space
- map space
- anchor space
- local model space
- screen or UI space

Do not debug a spatial bug until you know which coordinate frame is first wrong.

### Spatial Debugging Rules

- Always identify the first wrong coordinate transform.
- Log anchor IDs, transform sources, and frame assumptions explicitly.
- Distinguish:
  - wrong stored geometry
  - wrong transform application
  - stale session state
  - stale rendered guidance
- Never debug a rendered AR overlay without confirming the underlying persisted or derived geometry first.

### AR Session And Mapping State

For AR or mapping apps, keep one clear source of truth for:

- rooms
- anchors
- graph nodes
- route edges
- calibration data
- active route state

Rendered guidance, arrows, or labels should be derived from that state, not stored as independent truth.

### Device Validation

- Simulator is not sufficient for camera, AR, tracking, motion, or sensor correctness.
- If a task changes tracking, calibration, world mapping, path rendering, or device pose interpretation, require on-device validation before closing the task.

## Planning File Rules

### Cleanup On New Task Start

- Before adding new work, remove completed or stale items.
- Remove:
  - `[x]`
  - `[~]`
  - `completed`
  - `done`
  - `superseded`
  - `scheduled`
- Keep only active items.

### Required Todo Format

- Every planning file must include `## Open items`.
- Open items must use unchecked checkboxes only:
  - `- [ ] <task>`
- Items must be short, actionable, and implementation-focused.

## Execution Safety

- Never do implementation work on `main` or `master`.
- If the current branch is `main` or `master`, stop and move all uncommitted changes onto a new branch before continuing.
- Use conventional branch naming with a lowercase slash prefix plus a short kebab-case slug.

Preferred prefixes:

- `feature/`
- `fix/`
- `chore/`
- `docs/`
- `refactor/`
- `spike/`

Examples:

- `fix/task-detail-refresh`
- `refactor/form-save-flow`
- `docs/api-overview-refresh`

### Command Sequencing

- Default all commands to sequential execution.
- Run commands in parallel only when they are independent and read-only.
- Never run mutating commands in parallel:
  - git/worktree/index writes
  - file writes
  - build artifacts
  - caches
  - derived data
- Run build, test, and code generation commands sequentially.

### Git Safety

- Run these sequentially only:
  - `git add`
  - `git rm`
  - `git mv`
  - `git commit`
  - `git merge`
  - `git rebase`
  - `git cherry-pick`
  - `git checkout`
  - `git stash`
  - `git reset`
  - `git clean`
- If a Git command fails due to `index.lock`, stop, remove the stale lock, and retry the same command sequentially.

## Pre-Commit Checkpoint

- Do not commit unless the user explicitly asks.
- Before every commit:
  - run `git status --short`
  - confirm only intended files are staged
  - run the commit command sequentially

## Agent RAM Persistence Protocol

### Why

Agents keep two kinds of context:

- Disk context: repo state that persists through git
- RAM context: the plan, decisions, and in-progress state that can be lost on interruption

The goal is to make work restart-safe by writing RAM context into the repo.

## Where It Lives

Put continuity files in `.agents/`:

- `.agents/state.md` is the source of truth for the current task state

Keep it committed and current while working on a feature branch.

## `.agents/state.md` Template

```md
# State Capsule

## Plan

- [x] Step already done
- [~] Step in progress — brief note on exactly where it stopped
- [ ] Step not started yet
- [ ] Step not started yet

## Last known state

tests green / build failing / untested

## Decisions (don't revisit)

- <decision> — <why>

## Files touched

- path
```

## Operating Procedure

### Start Of Every Run

1. Read `.agents/state.md`.
2. Run `git status` and `git log --oneline -5` to orient.
3. Resume from the `[~]` step if present, otherwise the first `[ ]` step.

### During Work

- Before starting each step, mark it `[~]`.
- After completing each step, mark it `[x]`.
- After any test or build run, update `Last known state:`.
- When a non-obvious decision is made, add it to `Decisions` immediately.

### On Stop

No special shutdown procedure is required if `.agents/state.md` was kept current during the task.

## Memory Lifecycle

- `.agents/` is branch-scoped.
- It should exist on feature branches, not on `main`.
- Each feature branch owns its own `.agents/` state.
- If the branch is abandoned, delete stale `.agents/` files rather than carrying misleading state forward.

If your repo has automation that purges `.agents/` on `main`, document it here. Otherwise, add a short cleanup rule for humans.

## iOS Test Policy

- Default to `swift test` for package and library work when that is the project norm.
- If a task changes app UI or app behavior in `[AppPaths/**]`, run the relevant app build before finishing even if the user did not explicitly ask for it.
- If your repo has a post-merge iOS regression workflow, document it here:
  - `[RegressionWorkflowName]`
- If certain core files, macro paths, geometry engines, routing code, or session-management code imply extra regression risk, call that out explicitly in the task plan.
- If a task changes AR, camera, tracking, location, motion, or calibration behavior, note that device validation is required.

## Customization Notes

Projects should customize at least these sections:

- Scoped TDD paths
- App/UI build command
- Bug-solving playbook path if you keep a separate dedicated doc
- Regression workflow name
- Domain-specific payload or persistence rules
- Device-validation rules
- Spatial or sensor-state rules if relevant

Avoid adding repo-specific architecture rules here unless they are stable enough to be useful across many tasks.
