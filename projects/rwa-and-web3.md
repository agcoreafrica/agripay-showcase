# RWA & Web3 systems — Guarded architecture briefs

> **Tier 2 — Guarded architecture briefs.** Stack, scale, and architectural scope are described;
> source stays private. No key scripts, audit artifacts, or internal paths appear on this page.

---

## XMetals & XEstates

Real-world-asset (RWA) tokenization platforms for precious metals and real estate, built for the
XRPL EVM Sidechain with RLUSD as the settlement asset. XEstates is the predecessor lineage;
XMetals is the active line the portfolio counts.

**Scale & stack**

- XMetals (active line): ~154,300 LOC — TypeScript, Solidity, SQLite WAL, React Vite
- 127 Solidity contracts; 72 test files
- XEstates (predecessor lineage): ~118,000 LOC — 100 Solidity contracts; Next.js 15, RainbowKit, wagmi, Firebase
- Mobile-money fiat relay bridging local payment rails to on-chain settlement
- Generated ABI SDK so client applications consume typed contract interfaces instead of hand-rolled ABI glue

**Architectural scope**

- Full RWA lifecycle: asset representation, issuance, and investor-facing applications across web and mobile surfaces
- Two independent contract lineages — metals and real estate — the second designed with the lessons of the first
- Fiat on/off-ramp integration with mobile money treated as a first-class rail, not a bolt-on
- Contract surface exposed to client applications through a generated, typed SDK

**Withheld:** deployment key scripts, audit artifacts, internal paths, and source code.

---

## Caveman

Non-custodial commit-reveal on-chain game on the XRPL EVM Sidechain — built to demonstrate that
gameplay randomness can be made fair and unforgable on-chain without custodial trust.

**Scale & stack**

- ~31,000 LOC
- Solidity 0.8.37 (Cancun), Foundry, wagmi/viem, Three.js
- 6 core contracts + 21 Foundry test/script files

**Security engineering on display**

- **Commit-reveal randomness** — commits are binding before any reveal exists, so neither player nor contract owner can steer outcomes
- **Pull-payments** — value leaves the contract only at the claimant's initiative; funds are never pushed into arbitrary call frames
- **Reentrancy guards** on every state-changing money path
- **Exploit proofs-of-concept written as tests** — the attack classes the design defends against are executable documentation, not prose
- **Invariant fuzzing** over core state transitions

**Withheld:** audit findings, deployment and secret scripts, and source code.
