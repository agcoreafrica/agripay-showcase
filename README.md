# Agcore Africa — Engineering Portfolio

> **12 active/shipped systems · ~1.75M+ lines of deduplicated source · 1,700+ test files and suites**
>
> Production fintech and settlement rails. Offline-first field systems that run with no connectivity.
> Applied AI with hard agentic guardrails. RWA smart contracts. Precision-ag geospatial and computer
> vision. Mechanistic scientific simulation with sealed, preregistered experiment harnesses.

## Engineering profile

Agcore Africa builds systems for environments where failure is expensive: money that must not
double-pay, field operations that must run without connectivity, AI that must not act without
authority, and contracts that must not lose custody of value. This portfolio is the curated public
surface of that work — architecture briefs, verified scale numbers, and (for the flagship system)
abridged code highlights — with proprietary systems deliberately locked.

| Profile area | One-line summary |
|---|---|
| Production Fintech & Settlement | Dual-currency, multi-leg settlement with per-leg idempotency, resumable partial success, and fail-closed rails health |
| Offline-First Systems | One queued envelope fanning out to ten typed server-action surfaces; zero-connectivity payments and peer-to-peer communication |
| Applied AI & Agentic Guardrails | Propose-only copilots, payload-hash-bound four-eyes approvals, grounded citation verification, graph-gated act/draft/refuse boundaries |
| RWA Smart Contracts | 227 Solidity contracts across two real-world-asset tokenization lineages (precious metals & real estate) plus a non-custodial commit-reveal game |
| Precision Ag / Geospatial | Tenant-scoped satellite raster serving, drone-mission field apps, computer-vision and video-sampling microservices |
| Scientific Simulation | A 35-state ODE + Monte Carlo bio-economic engine with a preregistered, SHA-256-sealed experiment harness; a zero-training reasoning engine |

## Capabilities at a glance

Six engineering pillars, each proven in more than one shipped system:

| Pillar | Proof points across the portfolio |
|---|---|
| **Distributed Systems & Money Rails** | AgriPay: dual-currency handoff settlement with per-leg idempotency keys and resumable partial success; a pure cycle-settlement waterfall; rails health that fails closed in production · XMetals: mobile-money fiat relay and a generated ABI SDK over an EVM sidechain · Quantitative Market Feed & Backtesting Suite: multi-source live market/oracle ingestion under a process watchdog |
| **Offline-First & Cross-Platform Mobile** | AgriPay: offline sync endpoint fanning one envelope to ten typed surfaces with deterministic idempotency · WakaMobile / OffNet (locked): zero-connectivity payments and peer-to-peer communication across native Android, native iOS, Node.js, and Python simulation · Precision Viticulture: Capacitor 8 iOS/Android field app with BLE, GPS, and camera integration |
| **Applied AI, RAG & Agentic Guardrails** | AgriPay: propose-only copilot whose consequential approvals are cryptographically bound to the exact payload hash · Reach AI: grounded citation verification and human-in-the-loop approval gates behind a first-party MCP server · Signet AI: citation-enforced RAG · AMES: graph-gated act/draft/refuse execution boundaries after local post-training · CareMatch: two-stage LLM personalization with scenario-matrix testing |
| **Smart Contracts & Security Testing** | XMetals / XEstates: 127 + 100 Solidity contracts across the lineage with generated, typed SDKs · Caveman: commit-reveal randomness security, pull-payments, reentrancy guards, exploit proofs-of-concept written as tests, and Foundry invariant fuzzing |
| **Geospatial, Computer Vision & IoT** | AgriPay: entitlement-gated, tenant-scoped satellite raster serving that degrades to a named gap instead of fabricating data; MQTT telemetry · Precision Viticulture: Cloud Run Python computer-vision and video-sampling microservices wired to drone missions |
| **Verification & Reliability Culture** | 1,700+ test files and suites portfolio-wide · AgriPay: 1,110 test files, emulator-backed security-rules tests, 26 Playwright e2e specs, three AI eval harnesses, a citation rot-gate in CI · Livestock & Bioconversion: preregistered, SHA-256-sealed reproducible experiment harness · DITCH-0: ablation-verified modular testing |

## Verified portfolio footprint

Approximately **1.75M+ deduplicated source lines** and **1,700+ test files and suites** across
**12 active/shipped systems**, plus one public research workbench and one lightweight utility.
Counting rule: where a codebase forked or evolved (XEstates → XMetals; an earlier WakaMobile
prototype), the lineage is counted cleanly on its active line rather than double-counted.

| # | System | Tier | Approx. source LOC | Verification |
|---|---|---|---|---|
| 1 | AgriPay OS | 1 — Public deep dive | ~795,000 (TypeScript, incl. 203,000 test LOC) | 1,110 test files; 26 Playwright e2e specs; 3 AI eval harnesses |
| 2 | Precision Viticulture & Orchard Operations Platform | 2 — Guarded brief | ~247,000 | 246 Jest test files |
| 3 | Reach AI | 2 — Guarded brief | ~185,800 | 204 test files |
| 4 | XMetals / XEstates (RWA lineage) | 2 — Guarded brief | ~154,300 active line | 72 test files; 127 Solidity contracts |
| 5 | WakaMobile / OffNet | 3 — Locked | ~114,600 active build | 39 test suites incl. penetration, conformance, mobile instrumentation |
| 6 | AMES | 2 — Guarded brief | ~47,300 core | 40 test files |
| 7 | DITCH-0 | 3 — Locked | ~40,200 | 32 unittest suites / 282 unit tests |
| 8 | CareMatch | 2 — Guarded brief | ~37,100 | 13 test suites |
| 9 | Caveman (on-chain game) | 2 — Guarded brief | ~31,000 | 6 core contracts + 21 Foundry test/script files |
| 10 | Signet AI | 2 — Guarded brief | ~30,600 | 22 test files |
| 11 | Livestock & Bioconversion Simulation Engine | 3 — Locked | ~20,800 owned | Preregistered, SHA-256-sealed experiment harness |
| 12 | Quantitative Market Feed & Backtesting Suite | 3 — Locked | ~11,200 | 7 test suites |
| + | Ifa Knowledge Graph (public research workbench) | 1 — Public deep dive | ~48,700 | Page-aligned forensic citation auditing; statistical testing |
| + | Local Agent Web Wrapper (lightweight utility) | 1 — Public | ~2,900 | — |

Predecessor lineages held out of the active-line counts above: XEstates (~118,000 LOC, 100 Solidity
contracts) and an earlier WakaMobile prototype (~26,000 LOC). Both are shown for lineage context on
their project pages — never double-counted in the footprint.

## Project directory — organized by IP protection tier

### Tier 1 — Public deep dives (architecture + abridged code highlights)

Full architectural narrative, and abridged source excerpts you can read right here.

- **AgriPay OS** — the operating system for agricultural organisations: settlement engine, evidence
  and four-eyes approvals, AI copilot guardrails, offline sync, satellite layer serving, testing
  and CI. Start at [ARCHITECTURE.md](ARCHITECTURE.md), then the six highlights:
  [01 Settlement engine](highlights/01-settlement-engine.md) ·
  [02 Evidence and four-eyes](highlights/02-evidence-and-four-eyes.md) ·
  [03 AI copilot](highlights/03-ai-copilot.md) ·
  [04 Offline sync](highlights/04-offline-sync.md) ·
  [05 Satellite layers](highlights/05-satellite-layers.md) ·
  [06 Testing and CI](highlights/06-testing-and-ci.md)
- **Ifa Knowledge Graph** — provenance-tracked computational knowledge graph and comparative
  research workbench for the 256 Odu Ifa corpus, with forensic page-aligned citation auditing →
  [projects/public-systems.md](projects/public-systems.md)

### Tier 2 — Guarded architecture briefs (stack, scale & architectural scope; source private)

What each system does, its stack, its scale, and its architectural scope — no source code.

- **XMetals & XEstates** — RWA tokenization for precious metals and real estate on the XRPL EVM
  Sidechain with RLUSD → [projects/rwa-and-web3.md](projects/rwa-and-web3.md)
- **Caveman** — non-custodial commit-reveal on-chain game with executable exploit PoCs and
  invariant fuzzing → [projects/rwa-and-web3.md](projects/rwa-and-web3.md)
- **Reach AI** — multi-tenant B2B intelligence & outreach automation with a first-party MCP
  server → [projects/ai-and-agents.md](projects/ai-and-agents.md)
- **Signet AI** — enterprise RAG/A2UI server and multi-modal content studio →
  [projects/ai-and-agents.md](projects/ai-and-agents.md)
- **AMES** — local LLM post-training, alignment & agentic governance harness →
  [projects/ai-and-agents.md](projects/ai-and-agents.md)
- **Precision Viticulture & Orchard Operations Platform** — hybrid web + native field and
  drone-mission platform with CV/video-sampling microservices →
  [projects/field-and-care-platforms.md](projects/field-and-care-platforms.md)
- **CareMatch** — caregiver–care-receiver compatibility and care-planning platform →
  [projects/field-and-care-platforms.md](projects/field-and-care-platforms.md)

### Tier 3 — [LOCKED — Private IP] (capability & scale summaries only)

Zero code or mechanism disclosure. Source, protocol mechanics, and internal designs are withheld;
available under NDA or as a live technical walkthrough during interviews.

- **WakaMobile / OffNet** — resilient zero-connectivity payments and peer-to-peer communication →
  [projects/locked-systems.md](projects/locked-systems.md)
- **DITCH-0** — zero-training neurocognitive reasoning engine in pure Python stdlib →
  [projects/locked-systems.md](projects/locked-systems.md)
- **Livestock & Bioconversion Simulation Engine** — mechanistic 35-state ODE + Monte Carlo
  bio-economic simulation → [projects/locked-systems.md](projects/locked-systems.md)
- **Quantitative Market Feed & Backtesting Suite** — multi-source live market/oracle ingestion,
  backtesting, watchdog, monitoring dashboard →
  [projects/locked-systems.md](projects/locked-systems.md)

## IP & confidentiality notice

This portfolio is tiered on purpose:

- **Tier 1 (public deep dives)** — the architecture narrative and abridged, sanitized excerpts
  needed to judge engineering standard. Nothing here is copyable into a product; it is published
  as evidence of practice.
- **Tier 2 (guarded briefs)** — stack, scale, and architectural scope only. Source code, key
  scripts, audit artifacts, and internal paths stay private.
- **Tier 3 (locked)** — capability and scale summaries only. These systems are proprietary
  platform IP or were built inside confidential engagements, so source code, protocol mechanics,
  internal designs, partner identities, and operational details are withheld entirely. The page
  states *what* each system does and *how rigorously it is verified* — never *how it works*.

Recruiters and hiring teams: every locked system can be walked through live under NDA during the
interview process — architecture, design decisions, and, where the engagement permits, code. Ask,
and we will schedule it.

---

*© 2026 Agcore Africa. See [LICENSE.md](LICENSE.md).*
