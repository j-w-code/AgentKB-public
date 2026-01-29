# AgentKB Roadmap

> **PUBLIC BASELINE** — This document describes the frozen public release (v0.5.1). Ongoing development continues in private repository.

---

## Public Release Status

**Version:** v0.5.1 (Phase 2.9.5)  
**Status:** Public Baseline — Frozen  
**Released:** January 2026

> ⚠️ **Note:** This public release represents a complete, production-ready governance layer. Phases 3+ continue in private development. Commercial licensing available for access to ongoing development.

---

## What's Included (Public Release)

### Phase 1: Output Gate MVP — ✅ Included

- `agentkb gate` — Output Gate for draft screening (PII, secrets, temporal claims)
- `agentkb scan` — PII/secret discovery across corpus
- `agentkb chat` — Gated LLM conversation (Ollama, Anthropic, OpenAI, xAI)
- `agentkb doctor` — Preflight diagnostics
- RBAC sensitivity ceiling enforcement
- REST API (`/v1/gate`, `/v1/chat`, `/v1/doctor`)
- MCP server for tool integration
- CI pipeline (tests, lint, security scan)

### Phase 2: Governance Enhancement + Audit — ✅ Included

- **GCS (Governance Compliance Score)** — Quantifiable governance metric with decision gates
- **Audit query interface** — `agentkb audit` CLI + REST endpoint
- **Evidence enforcement** — Block unsourced factual claims
- **Context integrity** — Session gate with drift detection
- **3-tier semantic detection** — Presidio + embeddings + optional LLM evaluator
- **Performance benchmarks** — NFR validation for latency/throughput

### Phase 2.5-2.9.5: Structural Enforcement — ✅ Included

- **Access Gate primitive** — Input validation with nested Tool Invocation Gate
- **Audit Bus** — Event coordination for gate-to-gate communication
- **Four Operational Modes** — FULL, SOLO-OG, SOLO-AG, ISLAND (gate independence)
- **Audit metrics API** — Structured metrics (blocks/day, rule coverage)
- **Locale-aware PII** — Regional pattern configurations
- **Adversarial test corpus** — Encoding attacks, prompt injection, tool exfiltration
- **Degraded mode handling** — Graceful failover when gates are unhealthy

**Framework alignment:** NIST AI RMF 1.0, OWASP Agentic Top 10 2026

---

## Private Development (Not Included)

### Phase 3: Access Control + Closed-Loop — 🔒 Private

- **Content-layer RBAC** — Retrieval-time enforcement (not just output)
- **Claim type filtering** — Scope what agents can access by claim type
- **Closed-loop CLI** — `error-analyze`, `rule-propose`, `rule-apply`
- **Multi-tenant isolation** — KB-scoped roles + tenant context

### Phase 4: Enterprise + Ecosystem — 🔒 Private

- **IdP integration** — OIDC/SAML → AgentKB roles
- **Tamper-proof audit** — Cryptographic signing for compliance
- **Python/JavaScript SDKs** — Native integration libraries
- **Audit dashboard** — Web UI for governance visibility
- **Meta-governance** — Rule lifecycle (retire obsolete rules)

---

## Licensing

| Access Level | What You Get |
|--------------|-------------|
| **Public (this repo)** | Phase 1-2.9.5 complete • Source-available • Evaluation/development use |
| **Commercial License** | Phase 3+ access • Production rights • Priority support |

---

## Contact

**J.W.** — Founder  
📧 agentkb_jw@proton.me

- Commercial licensing inquiries
- Design partner opportunities
- Technical feedback

---

*© 2025-2026 J.W. All rights reserved.*
