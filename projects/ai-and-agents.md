# Applied AI & agentic systems — Guarded architecture briefs

> **Tier 2 — Guarded architecture briefs.** Stack, scale, and architectural scope are described;
> source stays private. No prospect data, target institutions, legal documents, or cloud project
> identifiers appear on this page.

---

## Reach AI

Multi-tenant B2B intelligence and outreach automation platform, exposed to agents through a
first-party MCP server.

**Scale & stack**

- ~185,800 LOC; 204 test files
- TypeScript on Node.js 22, Express 5
- Model Context Protocol (MCP) SDK; Gemini
- Firestore knowledge graph; SQLite
- Stripe billing; Puppeteer

**Architectural scope**

- Multi-tenant B2B isolation over a knowledge-graph backbone
- Grounded citation verification: generated intelligence and outreach outputs are checked against their sources rather than trusted
- Human-in-the-loop approval gates on consequential outbound actions
- SaaS billing, entitlements, and an audit surface built on Stripe
- Automated web-facing research flows via headless browsing

**Withheld:** prospect lists, cloud project identifiers, and source code.

---

## Signet AI

Enterprise RAG/A2UI server and multi-modal content studio.

**Scale & stack**

- ~30,600 LOC; 22 test files
- TypeScript; Gemini; Google Cloud Storage
- Playwright + Vitest verification layers

**Architectural scope**

- Citation-enforced RAG: answers carry machine-checkable citations, or they do not ship
- A2UI surface: model-driven UI generation with automated verification behind it
- Automated storyboard and video rendering pipeline for multi-modal content production
- Browser-level UI verification via Playwright as part of the standard test suite

**Withheld:** target institutions, legal documents, cloud project identifiers, and source code.

---

## AMES

Local LLM post-training, alignment, and agentic governance harness — the full loop, from training
to governed execution, on local hardware.

**Scale & stack**

- ~47,300 core LOC; 40 test files
- Python 3.11; PyTorch; LoRA adapter training across DAPT, SFT, and DPO stages
- llama.cpp with GGUF quantization for local inference
- TypeScript consequence engine

**Architectural scope**

- End-to-end adapter pipeline: domain-adaptive pretraining → supervised fine-tuning → preference optimization → quantized local deployment → evaluation
- A consequence engine that gates agent behaviour into act / draft / refuse execution boundaries derived from a graph of action effects
- Alignment measured by evaluation harnesses rather than anecdotes
- Demonstrates full ownership of a model's behaviour on constrained hardware: train, align, quantize, govern

**Withheld:** training data, internal evaluation results, and source code.
