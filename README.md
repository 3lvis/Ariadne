# Ariadne

Ariadne is a local-first iPhone app for indoor AR navigation.

The current project scope is intentionally narrow: prove that one device can localize from a fixed start point, select a saved destination, and guide the user through one real indoor space. The broader MVP then extends that same flow to two spaces: `Home` and `Work`.

## Project goals

- map one floor per space
- use a fixed QR-coded start point for each space
- save named destinations locally on device
- guide the user from the start point to a selected destination with simple AR cues
- recover from failure by returning the user to the start point and retrying

## Current scope

The repository currently defines two planning tracks:

- the smallest real-world validation spike in [docs/planning/ariadne-tech-spike.md](/Users/nunez/code/ios/Ariadne/docs/planning/ariadne-tech-spike.md)
- the broader two-space MVP in [docs/planning/ariadne-mvp-plan.md](/Users/nunez/code/ios/Ariadne/docs/planning/ariadne-mvp-plan.md)

The higher-level project direction is summarized in [ariadne-app-plan.md](/Users/nunez/code/ios/Ariadne/ariadne-app-plan.md).

## Product shape

For MVP, Ariadne is:

- local-first
- single-device
- one floor per space
- one fixed start point per space
- one mapper workflow and one navigator workflow in the same app

For MVP, Ariadne is not:

- a cloud-synced product
- a multi-user shared mapping system
- a multi-floor indoor navigation platform
- a generalized building management tool

## Technical direction

The current intended stack is:

- SwiftUI for app UI
- SwiftData for local persistence
- ARKit and RealityKit for AR anchoring and guidance
- AVFoundation for QR scanning when needed

Most app shell, persistence, and flow work should be testable in simulator. AR anchoring, relocalization, and guidance quality must be validated on an iPhone.

## Repo layout

- `Ariadne/`: app source
- `Ariadne.xcodeproj/`: Xcode project
- `docs/planning/`: planning documents
- `AGENTS.md`: repo-specific agent workflow rules

## Next step

The recommended next implementation target is the tech spike:

- one space: `Home`
- one start point: the door
- one destination: `office`
- one QR scan flow
- one persisted destination anchor
- one simple AR guidance primitive
