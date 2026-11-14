# Highlight 04 — Offline-first field sync

Field work happens where connectivity does not. Every client mutation can be queued locally and
drained later through a single polymorphic sync endpoint that validates, deduplicates, and
dispatches to typed server actions.

## Why it is interesting

The hard part of offline sync is not storing rows — it is making replay safe across **many
different mutation shapes** without writing ten bespoke endpoints. This design uses one
discriminated-union envelope, validated with Zod at the boundary, dispatched with a deterministic
idempotency key, and answered with the *server's* entity id so the client can rewrite dependent
rows it queued while offline.

## The trampoline

Source: `app/api/v1/internal/sync-pending-event/handler.ts` (abridged)

```ts
// Polymorphic offline sync trampoline …
//
// The service worker and the in-page sync coordinator both POST queued rows
// here. A row's `input_json` is one of six discriminated envelopes (see
// lib/offline/pending-events-store.ts). This module validates the envelope
// shape, dispatches it to the matching server action with a deterministic
// idempotency key, and reports the server entity id back so dependent rows
// can be rewritten locally.
```

The envelope kinds — each with its own Zod schema, including numeric/length bounds and optional
evidence links:

```ts
export const OfflineEnvelopeKindSchema = z.enum([
  "operation_event",
  "cluster_plan_actual",
  "farm_task_completion",
  "watch_inspection",
  "plot_boundary_capture",
  "estate_chain_event",
  "smallholder_enrolment",
]);
```

## One envelope, ten typed surfaces

The endpoint (`app/api/v1/internal/sync-pending-event/route.ts`, abridged — imports shown are the
real dispatched surfaces) fans each validated envelope out to ten typed server actions:

- `recordOperationEventAction` — raw operation events (the evidence substrate)
- `recordClusterPlanActualAction` — production actuals against a plan
- `completeFarmTaskAction` — field task completion
- `createPlotAction` / `updatePlotBoundaryAction` — plot registry and geometry
- `enrolSmallholderAction` — enumeration enrolment
- `recordEstateHarvestLotAction` — harvest lots
- `createEstateDispatchAction` / `recordEstateIntakeAction` — estate chain of custody
- `executeOperatorOrgWorkbenchAction` — the operator workbench surface

## Safety properties

- **Deterministic idempotency keys** — a retried sync can never double-apply a row.
- **Boundary fingerprints** — plot geometry is hash-stable, so a boundary re-capture keeps its
  evidentiary linkage instead of forking a new identity.
- **Evidence in the same envelope** — task completions can carry up to 20 evidence operation-event
  ids, and the endpoint mints storage upload URLs so photos and records land together.
- **Entity-id round-trip** — the client learns the server's id for each applied row and rewrites
  dependent queued rows, so an offline chain (plot → boundary → harvest lot) replays in order.
