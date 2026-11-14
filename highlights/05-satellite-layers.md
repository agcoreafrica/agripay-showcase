# Highlight 05 — Tenant-scoped satellite layer serving

Satellite overlays for an estate are entitlement-gated, tenant-scoped, cached, and — when the
render environment is unavailable — answered with an honest `503` and a *named gap*, never a
fabricated image.

## Why it is interesting

Geospatial features in multi-tenant products leak in two directions: imagery served across tenant
boundaries, and "graceful" fallbacks that show the wrong field. This route closes both: rendered
rasters live under `tenant-<plan>` storage paths (never public static), the render bounding box is
cached with the overlay document so repeated requests never re-walk the plan's plots, and
degradation is explicit — a named gap the UI can explain, not synthetic data.

Source: `app/api/estate/layers/route.ts` (abridged)

```ts
// GET /api/estate/layers?plan=<planId>&layer=<id>[&format=png]
//
// Estate satellite overlays — entitlement-gated (any live trial or paid tier
// unlocks satellite_intelligence, per the founder decision 2026-09-24) and
// tenant-scoped: rendered PNGs live under tenant-<plan> Storage paths, never
// public static. The plan's plot union bounds drive the render; the bbox is
// cached with the overlay doc so repeated requests don't re-walk plots.
//
// Serving: default JSON (bounds, period, gaps, sidecar, pngUrl=self+format=png);
// format=png streams the cached/rendered raster. On-demand rendering follows
// the same env gate as /api/satellite/overlay — without the venv the route
// answers 503 with a named gap, never a fabricated overlay.
```

## Design points

- **Entitlement gate, not an honour system:** the route resolves the viewer's estate plan and
  checks the estate's entitlements before any storage or render work happens.
- **Tenant-scoped storage:** every rendered PNG is written under a `tenant-<plan>` prefix; there
  is no public bucket path to guess.
- **Cached bbox:** the plot-union bounds are computed once and cached with the overlay document.
- **JSON-first contract:** the default response is metadata (bounds, period, gaps, sidecar, and a
  self-referential PNG URL); `format=png` streams the cached or on-demand-rendered raster.
- **Honest 503:** if the render toolchain is missing, the route names the gap. A downstream client
  can distinguish "no data yet" from "data unavailable" — and neither from fabricated imagery.
