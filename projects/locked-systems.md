# Locked systems — [LOCKED — Private IP]

> **[LOCKED — Private IP]** The four systems on this page are documented at **capability and
> scale level only**. Source code, protocol mechanics, internal designs, and operational details
> are deliberately withheld. Each is available for review under NDA or as a live technical
> walkthrough during interviews — ask.

**Why this page looks the way it does.** These systems are proprietary platform IP or were built
inside confidential engagements. Describing *how* they work would disclose that IP, so this page
describes *what* each system does, *how large* it is, and *how rigorously it is verified* — and
nothing more. Every entry below intentionally omits mechanisms, protocols, and internal structure.

---

## WakaMobile / OffNet

> **[LOCKED — Private IP]**

Resilient zero-connectivity payments and peer-to-peer communication platform — built for
environments where connectivity cannot be assumed and payment integrity cannot be compromised.

**Scale & stack**

- Native Android (Kotlin/Java), native iOS (Swift), Node.js backend, and a Python simulation harness
- ~114,600 LOC in the active build (an earlier ~26,000 LOC prototype is lineage context, not double-counted)
- 39 test suites, including penetration, conformance, and mobile instrumentation tests

**Capabilities demonstrated**

- Cross-platform offline-first architecture spanning four codebases: two native mobile clients, a backend, and a simulation harness
- State-machine reconciliation between devices and backend, so state cannot silently diverge
- Hardware-backed key integrity on mobile devices
- Fault and network simulation as a first-class engineering tool, not an afterthought
- Security test engineering: penetration and conformance suites run as part of the build itself

**Withheld by design:** partner names, event and operational context, transport and protocol
mechanics, routing schemes, file layout, and all source code.

---

## DITCH-0

> **[LOCKED — Private IP]**

Zero-training neurocognitive reasoning engine, implemented in 100% Python standard library — no
ML frameworks, no pretrained weights, no external dependencies.

**Scale & stack**

- ~40,200 LOC of pure Python stdlib
- 32 unittest suites / 282 unit tests

**Capabilities demonstrated**

- Online system identification from streaming observation
- Symbolic law induction
- Risk gating around induced behaviour
- Multi-domain evaluation harnesses
- Ablation-verified modular testing — every module's contribution is measured by removing it

**Withheld by design:** internal mechanisms and heuristics, internal naming, file layout, and all
source code.

---

## Livestock & Bioconversion Simulation Engine

> **[LOCKED — Private IP]**

Mechanistic biological and techno-economic simulation platform: a 35-state ODE core with Monte
Carlo uncertainty propagation, spanning the biology and the economics in one model.

**Scale & stack**

- Python simulation core (NumPy/SciPy with LSODA integration); TypeScript calculation surface
- ~20,800 owned LOC
- Preregistered, SHA-256-sealed reproducible experiment & calibration harness — experiments are
  committed before they run and sealed so results cannot drift from protocol

**Capabilities demonstrated**

- Mechanistic (not curve-fit) biological modelling at production scale
- Monte Carlo techno-economic analysis layered over the mechanistic core
- Preregistration and sealing discipline — familiar from clinical and academic research — applied
  to software experiments and calibration

**Withheld by design:** equations, parameters, vendor model references, and all source code.

---

## Quantitative Market Feed & Backtesting Suite

> **[LOCKED — Private IP]**

Multi-source live market and oracle ingestion with quantitative backtesting, a process watchdog,
and a monitoring dashboard.

**Scale & stack**

- Python + Node.js
- ~11,200 LOC
- 7 test suites

**Capabilities demonstrated**

- Live multi-source market and oracle WebSocket ingestion, supervised by a process watchdog
- Quantitative backtesting infrastructure kept cleanly separate from live ingestion
- Operational monitoring dashboard for long-running processes

**Withheld by design:** strategy logic, arbitrage mechanics, alpha heuristics, and all source code.

---

## Interview access

Each locked system can be walked through live under NDA — architecture diagrams, design decisions,
and, where the engagement permits, code — without disclosing the withheld material above. See the
[ip & confidentiality notice](../README.md#ip--confidentiality-notice) in the README.
