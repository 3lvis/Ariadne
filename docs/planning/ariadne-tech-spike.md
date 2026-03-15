# Ariadne Tech Spike

## Purpose

This spike exists to prove the core technical loop on a real iPhone before building the broader MVP.

The question it answers is:

Can Ariadne localize from one fixed start point and guide the user to one saved destination in one real space?

## Spike definition

The spike is limited to:

- one space: `Home`
- one floor
- one fixed start point at the door
- one QR code at the start point
- one destination: `office`
- one iPhone
- local-only persistence

The spike flow is:

1. Open the app.
2. Scan the QR code at the door.
3. Start or restore the AR session from that fixed entry point.
4. Save one destination anchor for `office`.
5. Select `office`.
6. Show one simple guidance primitive toward the saved destination.
7. If localization fails, instruct the user to return to the start QR code and retry.

## Success criteria

The spike is successful when:

- the user can scan the QR code at the `Home` start point
- the app can save and reuse one destination anchor for `office`
- the app can guide the user from the start point to `office`
- the flow works across at least two separate runs on the iPhone
- failures are recoverable by returning to the start QR code

## Non-goals

The spike does not need:

- `Work` support
- multiple destinations
- search
- polished UI
- map editing UX
- backend sync
- multi-device support
- map versioning
- full routing graph support
- broad simulator coverage

## Technical focus

The spike should validate only the highest-risk technical pieces:

- QR scanning can reliably gate the start-point flow
- AR anchoring is stable enough in the real `Home` environment
- local persistence can store and restore enough spatial state for reuse
- guidance can remain understandable while walking to the destination
- relocalization failure can be detected and handled simply

## Recommended implementation cut

The fastest acceptable implementation is:

- one AR-focused screen
- one action to capture the start context
- one action to save the `office` destination
- one action to navigate to `office`
- one minimal persistence path for local spatial data
- one visual guidance primitive such as a beacon, marker, or arrow

## Open items

- [ ] Build a single-screen iPhone spike focused on one AR session and one destination flow.
- [ ] Implement QR scanning for the fixed `Home` start point at the door.
- [ ] Bind the scanned QR code to the start-point localization flow.
- [ ] Implement capture of one start-point anchor or equivalent relocalization context.
- [ ] Implement capture of one destination anchor for `office`.
- [ ] Persist the start-point and `office` destination data locally on device.
- [ ] Implement a minimal `Navigate to Office` action.
- [ ] Render one simple guidance primitive toward the saved `office` destination.
- [ ] Detect localization loss and show a retry instruction that sends the user back to the start QR code.
- [ ] Add diagnostics for anchor IDs, localization state, and transform source during spike runs.
- [ ] Validate the complete flow across at least two separate iPhone runs.
- [ ] Record what failed, what was stable, and what must change before moving into the broader MVP.
