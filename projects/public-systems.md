# Public systems

> **Tier 1 — Public deep dives / open works.** These works are documented openly. No code excerpts
> appear here — abridged excerpts live only in the AgriPay highlights
> ([01](../highlights/01-settlement-engine.md) – [06](../highlights/06-testing-and-ci.md)).

---

## Ifa Knowledge Graph

A provenance-tracked computational knowledge graph and comparative research workbench for the
256 Odu Ifa corpus — treating a large oral-textual corpus as a first-class dataset with forensic
provenance, rather than as folklore to be summarized.

**Scale & stack**

- ~48,700 LOC; 154 TypeScript source files
- Gemini / Vertex AI
- Custom SVG, PDF, and dashboard generators

**What makes it interesting**

- **Page-aligned forensic citation auditing** — every claim in the graph traces to the exact page of the source corpus, and dedicated tooling checks those alignments mechanically
- **Statistical testing** over comparative corpus questions instead of impressionistic reading
- **Self-generated reporting surfaces** — SVG visualizations, PDF documents, and dashboards produced by the system's own generators, so research output is reproducible from data

**Access:** documented openly as a research workbench; no proprietary client data involved.

---

## Local Agent Web Wrapper

A lightweight TypeScript WebSocket session wrapper for local agent sessions.

**Scale & stack**

- ~2,900 LOC
- WebSocket sessions over the `ws` library
- LiteLLM proxy orchestration for provider routing

**What it demonstrates**

- Small, sharp tooling: session lifecycle, transport, and proxy orchestration in ~3k lines
- Local-first agent infrastructure: agent sessions stay on the operator's machine, routed through a LiteLLM proxy for provider flexibility

**Access:** lightweight utility, described here at summary level.
