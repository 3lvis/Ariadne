# Ariadne App Plan

## Direction

This repository is currently scoped to a local-first MVP for two spaces: `Home` and `Work`.

The detailed MVP plan lives in `docs/planning/ariadne-mvp-plan.md`.

This file keeps only the higher-level app-plan work that is still active after that scope reduction.

## Open items

- [ ] Keep the current product scope aligned with the two-space local-first MVP in `docs/planning/ariadne-mvp-plan.md`.
- [ ] Choose the first-pass Apple frameworks for the MVP app shell, local persistence, QR scanning, and AR guidance.
- [ ] Define the reactive app architecture so views read local state and services own AR session, persistence, and routing behavior.
- [ ] Decide which deterministic geometry, routing, and persistence components require strict TDD coverage.
- [ ] Define the device-validation policy for AR tracking, mapping, relocalization, and route guidance changes.
- [ ] Create the initial module structure for domain models, AR session management, routing, persistence, and UI features.
- [ ] Build the minimal local-only spike that maps one start point, saves destinations, and guides to one room on-device.
- [ ] Define the first diagnostics set for coordinate-space, anchor, and relocalization bugs.
- [ ] Revisit backend sync, map versioning, and multi-user support only after the local MVP is proven on device.
