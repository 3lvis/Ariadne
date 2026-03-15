# Ariadne MVP Plan

## Product definition

Ariadne MVP is a local-first iPhone app for AR indoor navigation in two spaces: `Home` and `Work`.

Each space has:

- one floor
- one fixed start point
- one QR code at the start point
- a list of named destinations

The user selects the space in the app, scans the start QR code, chooses a destination, and receives AR guidance from the start area to the destination.

`Home` destinations for MVP:

- bedroom
- bathroom
- kitchen
- office

`Work` destinations for MVP:

- meeting rooms on one floor

## Scope decisions

In scope for MVP:

- local-only storage on one device
- one mapper workflow in the same app
- one navigator workflow in the same app
- one floor per space
- one start point per space
- destination list selection
- simple AR guidance using native-feeling waypoint or directional markers
- retry flow that sends the user back to the start QR code when localization fails

Out of scope for MVP:

- backend sync
- multi-device sharing
- multi-user relocalization
- search
- map version history
- editable indoor graph tooling
- multi-floor support
- generalized building management

## Success criteria

The MVP is successful when:

- the user can map `Home` from the door and navigate to bedroom, bathroom, kitchen, and office
- the user can map `Work` from a fixed entry point and navigate to saved meeting rooms on one floor
- the complete flow works on one iPhone without cloud services
- failures are recoverable by returning to the start QR code and retrying

## Technical direction

Recommended first-pass stack:

- SwiftUI for app UI
- SwiftData for local persistence
- ARKit and RealityKit for scanning, anchoring, and guidance
- AVFoundation for QR scanning if needed outside an AR session

Route modeling for MVP:

- persist one start anchor per space
- persist one destination anchor per room
- allow optional intermediate waypoints where direct guidance is not reliable
- derive displayed guidance from stored anchors and waypoints

Testing strategy for MVP:

- maximize simulator coverage for app shell, persistence, QR flow, and route-state logic
- require on-device validation for anchor placement, relocalization, and walking guidance

## Milestones

Milestone 1 goal:

- establish a simulator-friendly app shell and local data model

Milestone 2 goal:

- complete the `Home` mapping flow on device

Milestone 3 goal:

- complete the `Home` navigation flow on device

Milestone 4 goal:

- extend the same flow to `Work`

Milestone 5 goal:

- harden retry behavior, diagnostics, and remap workflow

## Open items

- [ ] Define the app module structure for UI, domain services, AR session coordination, and persistence.
- [ ] Define the SwiftData models for `Space`, `StartPoint`, `Destination`, `Waypoint`, and `SavedRoute`.
- [ ] Define the mapper flow state machine for idle, scanning, placing, saving, and failed states.
- [ ] Define the navigator flow state machine for idle, scanning QR, localizing, destination selection, guiding, and failed states.
- [ ] Implement the SwiftUI app shell with a space picker for `Home` and `Work`.
- [ ] Implement a destination list screen that reads local state and shows saved destinations for the selected space.
- [ ] Implement `Edit Map` mode in the same app behind a simple developer-facing entry point.
- [ ] Implement local persistence for one start point and multiple destinations per space.
- [ ] Implement a simulator-friendly fake localization mode so non-AR app flows can be exercised without walking.
- [ ] Implement QR scanning for the fixed start point and bind scans to the selected space.
- [ ] Implement on-device start-point capture for `Home` and persist it locally.
- [ ] Implement on-device destination capture for the four `Home` destinations and persist them locally.
- [ ] Implement optional waypoint capture for routes that need intermediate guidance.
- [ ] Implement AR guidance from the start point to a selected destination using waypoint or directional markers.
- [ ] Implement a failure flow that detects lost localization and instructs the user to return to the start QR code.
- [ ] Add diagnostics that log anchor IDs, transform sources, and localization state for failing navigation runs.
- [ ] Validate the full `Home` flow on device from QR scan to destination arrival.
- [ ] Implement on-device start-point capture for `Work` and persist it locally.
- [ ] Implement on-device destination capture for `Work` meeting rooms on one floor.
- [ ] Validate the full `Work` flow on device from QR scan to meeting-room arrival.
- [ ] Document the exact remap procedure for replacing a saved `Home` or `Work` map from scratch.
