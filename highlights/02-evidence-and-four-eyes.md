# Highlight 02 — Evidence and four-eyes approval

The AI copilot can *propose* consequential actions — payments, settlements, recalls, carbon
finalisation — but can never *decide* them. Proposals become durable drafts whose approvals are
cryptographically bound to the exact payload, executed under a single transactional claim, and
reported `uncertain` rather than silently re-run when things go wrong.

## Why it is interesting

Most "AI agent" demos hand the model tools and hope. This system treats an AI proposal the way
banks treat a payment instruction: hash-bound, dual-controlled, single-use, and fully audited.
The guarantees are enforced in the repository layer, not in prompts.

## The draft lifecycle

Source: `lib/operator-org/agent-drafts.ts` (abridged)

```ts
// Agent action draft repo — durable, scope-bound AI action proposals.
//
// Lifecycle: draft → (approve|reject) → executing → executed|failed|uncertain,
// with expiry. Guarantees enforced here:
// - Decisions bind to the exact payload hash: an approval recorded against
//   one payload can never authorise a different one.
// - A single execution claim per draft (transactional); a finished draft
//   cannot be re-executed (replay), and a claimed-but-crashed draft stays
//   `executing` until the claim lapses — it is then reported `uncertain`,
//   never silently re-run.
// - Consequential drafts require a second, different person to approve.
```

## Payload-hash binding, derived from the catalog

The four-eyes rule is not a list maintained by hand — it is *derived* from the action catalog,
which is the single source of truth for the whole executable surface. Unknown action ids fall to
the safest default.

Source: `lib/operator-org/agent-drafts.ts` (abridged)

```ts
/** Consequential actions demand a four-eyes approval (approver ≠ requester).
 *  Derived from ORG_ACTION_CATALOG (single source of truth) — an action is
 *  consequential iff its catalog row says so; unknown actions are risk_bearing
 *  for the human path but are refused by the autonomy lane gate. */
export function riskForAction(actionId: string): AgentActionDraftRisk {
  return catalogActionById(actionId)?.risk === "consequential" ? "consequential" : "risk_bearing";
}

/** Autonomy lane for the mandate gate. Unknown ids resolve to the most
 *  restrictive lane, so the auto-decider can never execute what the catalog
 *  does not know. */
export function autonomyLaneForAction(actionId: string): OrgActionAutonomyLane {
  return autonomyLaneForCatalogAction(actionId);
}
```

The hash itself is a SHA-256 over a **stable stringification** — key-sorted, `undefined`-stripped,
recursive — so semantically identical payloads always hash identically:

```ts
function stableStringify(value: unknown): string {
  if (value === null || typeof value !== "object") return JSON.stringify(value) ?? "null";
  if (Array.isArray(value)) return `[${value.map(stableStringify).join(",")}]`;
  const entries = Object.entries(value as Record<string, unknown>).filter(([, v]) => v !== undefined).sort(([a], [b]) => (a < b ? -1 : 1));
  return `{${entries.map(([key, v]) => `${JSON.stringify(key)}:${stableStringify(v)}`).join(",")}}`;
}

export function hashDraftPayload(payload: Record<string, unknown>): string {
  return createHash("sha256").update(stableStringify(payload)).digest("hex");
}
```

## Autonomy lanes — the catalog decides what AI may touch

Every one of the 90+ catalogued actions carries its lane, baked onto the executable surface so
there are no parallel sets to drift out of sync:

Source: `lib/operator-org/action-catalog.ts` (abridged)

```ts
/** Autonomy lane — decides whether the autonomous-mandate engine may ever
 *  execute an action without a human decision. Baked onto the catalog so the
 *  executable surface is the single source of truth (no parallel sets).
 *  - auto_routine:  zero-touch under an active mandate within daily rate caps
 *  - auto_bounded:  zero-touch only inside the mandate's numeric/NGN envelope
 *  - counterparty_or_worker_only: evidentiary meaning requires the independent
 *    actor (field worker or resource owner); never org-auto-executed
 *  - human_consequential: hard-coded human sign-off; the autonomy engine may
 *    stage these drafts but can never decide or execute them
 */
export type OrgActionAutonomyLane =
  | "auto_routine"
  | "auto_bounded"
  | "counterparty_or_worker_only"
  | "human_consequential";
```

## Consent is first-class data

Supplier-farm consent is modelled as a versioned grant with three permissions, an
optimistic-concurrency guard, and a rule that re-consent **replaces — never unions** the selection
the farmer actually reviewed. Revocation is honored on every read.

Source: `lib/operator-org/supplier-consent.ts` (abridged)

```ts
export const SupplierPermissionSchema = z.enum(["satellite_monitoring", "map_farms", "production_evidence"]);
```

```ts
export function nextSupplierConsent(input: GrantSupplierConsentInput, farmerId: string, previous: SupplierFarmConsent | null, now: string): SupplierFarmConsent {
  if (previous && previous.revision !== input.expected_revision) throw new Error("consent_revision_conflict");
  // Re-consent replaces, never unions, the selection the farmer actually reviewed.
  return SupplierFarmConsentSchema.parse({
    // … full row construction elided …
  });
}
```

## Role capability matrix

Ten org roles map onto a typed capability array — authorization is a type-checked lookup, not a
string comparison buried in a page component:

Source: `lib/operator-org/policy.ts` (abridged)

```ts
export const ORG_ROLE_CAPABILITIES: Record<OperatorOrgRole, readonly OrgCapability[]> = {
  owner: [...ESTATE_TEAM_CAPABILITIES, "team", "sites", "provision", "evidence_release", "cycle_close"],
  country_lead: [...ESTATE_TEAM_CAPABILITIES, "sites", "evidence_release"],
  operations_manager: [...ESTATE_TEAM_CAPABILITIES, "sites", "evidence_release"],
  agronomist: ["operations", "certifications", "digital_twin"],
  supervisor: ["operations", "digital_twin"],
  storekeeper: ["stock", "procurement", "equipment"],
  processing_manager: ["operations", "logistics"],
  logistics_manager: ["logistics", "equipment"],
  finance: ["finance", "labour_pay", "commerce", "carbon", "contractors", "cycle_close"],
  viewer: [],
};
```

## Evidence-bound actuals

Field records are accepted only if the events were recorded by the worker's own identity, within
the same organisation and plan — the evidentiary chain is checked in a pure function:

Source: `lib/operator-org/actual-evidence.ts` (abridged)

```ts
export function orgWorkerEvidenceMatches(events: readonly Pick<OperationEvent, "person_id" | "metadata">[], requestedIds: readonly string[], scope: { person_id: string; org_id: string; plan_id: string }): boolean {
  return new Set(requestedIds).size === requestedIds.length && events.length === requestedIds.length && events.every((event) => event.person_id === scope.person_id && event.metadata?.org_id === scope.org_id && event.metadata?.cluster_plan_id === scope.plan_id);
}
```
