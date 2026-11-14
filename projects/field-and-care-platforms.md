# Field & care platforms — Guarded architecture briefs

> **Tier 2 — Guarded architecture briefs.** Stack, scale, and architectural scope are described;
> source stays private. No incident logs or internal secret modules appear on this page.

---

## Precision Viticulture & Orchard Operations Platform

A hybrid web + native field operations and drone-mission platform for vineyards and orchards:
office-grade planning in the browser, field-grade execution in the pocket, drones and computer
vision in the middle.

**Scale & stack**

- ~247,000 LOC across an extended monorepo; 246 Jest test files
- Next.js 14 web surface; Capacitor 8 native iOS/Android apps
- Cloud Run Python microservices for computer vision and video sampling
- Custom MCP servers wiring domain tools into agent workflows
- Clerk authentication; Firebase; Vertex AI
- Native device integration: BLE, GPS, camera

**Architectural scope**

- One platform across planning, execution, and verification: field crews, drone missions, and CV-derived sampling operate on the same records
- Drone missions as first-class operations, not bolt-on imagery
- Computer-vision and video-sampling microservices on Cloud Run, decoupled from the app so heavy inference never blocks field UX
- Custom MCP servers expose the platform's capabilities to agentic clients under governance
- Native hardware reach (BLE / GPS / camera) for conditions where connectivity and environment are hostile

**Withheld:** incident logs, internal secret modules, and source code.

---

## CareMatch

Caregiver–care-receiver compatibility and care-planning platform: matching, planning, and
coordination for continuing care.

**Scale & stack**

- ~37,100 LOC; 13 test suites
- Next.js 15, React 19
- Zod-validated contracts at every boundary
- Multi-provider LLM pipeline
- Firebase; App Engine

**Architectural scope**

- Two-stage LLM personalization: a structured compatibility/planning stage, then a narrative stage — each stage independently testable
- Deterministic scenario-matrix testing: the LLM pipeline is evaluated across a fixed matrix of care scenarios, so personalization quality is measurable rather than anecdotal
- Type-safe boundaries via Zod spanning client, server, and pipeline

**Withheld:** source code and operational data.
