# GED Bestie PR Handoff Guide

## Short answer
Yes — you can (and should) open the production PR in the **actual GED Bestie repository**.

I cannot directly modify GED Bestie from this workspace because this environment only contains the `Math-Challenge-Games-` repository.

## Which option is this?
This is **Option A** from the integration plan:
- Keep Math Challenge Games as an embedded web module.
- Use bridge events to connect gameplay telemetry/results to GED Bestie.
- Use `CONNECT_THE_DOTS.md` for an implementation-ready host bridge + API wiring sequence.

## Do we need to rewrite the plan?
No full rewrite is required.

Use the existing `INTEGRATION_PLAN.md` as the architecture reference and apply the already-implemented bridge hooks from this repo's `index.html` as the initial integration baseline.

## What to port into GED Bestie now
1. Mount this game as a web module route (for example: `/games/math-challenge`).
2. Expose a host bridge object:
   - `window.GED_BFF_BRIDGE.onGameStart(payload)`
   - `window.GED_BFF_BRIDGE.onQuestionAnswered(payload)`
   - `window.GED_BFF_BRIDGE.onGameComplete(payload)`
3. Persist payloads to GED Bestie services (attempt history, profile progress, analytics).

## Suggested PR checklist (GED Bestie repo)
- [ ] Add new route + shell container for Math Challenge Games.
- [ ] Provide `GED_BFF_BRIDGE` implementation in host app.
- [ ] Map events to backend endpoints.
- [ ] Add auth guard for ranked mode.
- [ ] Add analytics validation for all 3 lifecycle events.
- [ ] Add QA pass on mobile + keyboard input support.

## Suggested PR title
`feat(games): integrate Math Challenge Games as GED BFF module with telemetry bridge`

## Suggested PR description
- Embed Math Challenge Games in GED BFF under a dedicated games route.
- Implement `GED_BFF_BRIDGE` to consume `onGameStart`, `onQuestionAnswered`, and `onGameComplete` events.
- Persist game attempts and expose progress in learner profile analytics.
- Gate rollout behind a feature flag.

## Notes
If you want, I can next produce a **copy-paste PR body** tailored to the exact framework in GED Bestie (Next.js/React Native/etc.) once you share the target repo structure.
