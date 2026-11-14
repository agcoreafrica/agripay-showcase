# Highlight 01 — Settlement engine

Dual-currency, multi-leg money movement with per-leg idempotency, resumable partial success, a
pure cycle-settlement waterfall, and a fail-closed rails-health discipline.

## Why it is interesting

Payment orchestration in production fails in boring ways: a rail times out mid-split, a process
dies between legs, a retry double-pays. This engine is built so that none of those failures can
double a payout: every leg's idempotency key is derived from the handoff id and leg name, pending
or successful legs are returned without re-invoking the rail, and only failed legs are retried.
The two money events that newcomers always conflate — splitting one delivery's proceeds vs
closing a whole production cycle — are two separate modules with an explicit rule about why.

## The orchestrator

Source: `lib/handoffs/pay-settlement.ts` (abridged)

```ts
// Settlement orchestrator. Handles two currencies:
//
// NGN-denominated (total_amount_kobo): the cooperative's revenue_split is
// applied directly to kobo, each leg routed through the payment router. The
// producer leg gates the status transition to "settled"; cooperative and
// platform legs can lag without blocking the producer's release of funds.
//
// USDC-denominated (total_usdc_amount): a swap_usdc leg first off-ramps the
// stablecoin into NGN at the cooperative's swap-staging account; the
// resulting NGN total is then split per revenue_split and each NGN leg
// routes as in the kobo path. The handoff transitions to "settled" only when
// every leg (swap + producer + cooperative + platform) succeeds —
// partial-success leaves the handoff "witnessed" so a retry resumes from the
// persisted Payouts via idempotency_key.
//
// Idempotency: every leg's idempotency_key is `ho_<handoff_id>_<leg_name>`.
// Pending or successful prior legs are returned without re-invoking the rail;
// only failed legs are retried. This means a rerun with the same handoff_id
// is safe and never doubles a swap or a payout.
```

## The cycle waterfall — a pure function

Settlement of a full production cycle is computed with **no I/O at all**, which makes it trivially
testable: financier recovery is senior, then operator profit share and vested equity, then the
sponsor takes the remainder.

Source: `lib/cluster-os/cycle-settlement.ts` (abridged)

```ts
/**
 * Cluster cycle settlement — pure (no I/O).
 *
 * The plan lifecycle already ends `operating → closing → closed`, gated by
 * `assertPlanOperationsClosable`. What was missing is the money: nothing
 * computed what a closed cycle actually distributed, or to whom.
 *
 * This is NOT handoff settlement. `pay-settlement.ts` splits the proceeds of
 * ONE delivery between producer / cooperative / platform. A cycle settlement
 * closes the whole production cycle: it recovers the financier first, then
 * pays the named operators their profit share and their vested equity. Two
 * different money events; conflating them would pay the wrong people from the
 * wrong pot.
 *
 * Waterfall is financier-senior, matching the settlement rail the banks
 * already underwrite against:
 *
 *     gross revenue
 *       − platform fee
 *       − operating cost
 *       − financier recovery      ← senior; 0 for a grant-funded programme
 *       = distributable residual
 *           → operator legs (profit share + vested equity)
 *           → remainder to the sponsor
 *
 * …
 */
```

## Rails health — env presence, never values, fails closed

Ops can answer "is a rail live?" without reading deployment config, and the system refuses to
simulate money movement in production.

Source: `lib/integrations/payments/rails-health.ts` (abridged)

```ts
// Rails readiness snapshot — env presence only (no live HTTP pings).
// Ops answer "is Bitnob/Paystack real?" without reading Cloud Run config.
//
// Status meanings:
//   live          — credentials present AND mock path is off AND
//                   PAYMENTS_MOCK_MODE is not set (true production shape)
//   mock          — adapter would return mock success (dev/test), or
//                   PAYMENTS_MOCK_MODE=true is set (including forbidden prod)
//   missing       — credentials absent and mock is off → missing_credentials
//   misconfigured — production + PAYMENTS_MOCK_MODE=true (money fails closed;
//                   never label live)
```

```ts
export type RailStatus = "live" | "mock" | "missing" | "misconfigured";

export interface RailHealthRow {
  provider: string;
  role: "inflow" | "outflow" | "swap" | "anchor" | "stablecoin";
  status: RailStatus;
  /** Env var names that gate this rail (never values). */
  credential_env: string[];
  credentials_present: boolean;
  notes?: string;
}
```

## Tests around it

The settlement path is covered by dedicated unit tests, including the institution-ambiguity
invariant (a multi-portfolio case refuses only when a fee leg needs a unique institution, and the
decision set must be complete — no sampled rows) and a boundary test that strips client-supplied
fee fields at the action boundary.
