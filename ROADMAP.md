# AgentKB Roadmap

> **PUBLIC DOCUMENT** — High-level phases. Detailed specs in private development repo.

---

## Current Status

**Stage:** Phase 1-2 Public Release  
**Current Version:** v0.4.9 (January 2026)

---

## Phase 1: Output Gate MVP — ✅ Complete

**Delivered:**
- `agentkb gate` — Output Gate for draft screening (PII, secrets, temporal claims)
- `agentkb scan` — PII/secret discovery across corpus
- `agentkb chat` — Gated LLM conversation (Ollama, Anthropic, OpenAI, xAI)
- `agentkb doctor` — Preflight diagnostics
- RBAC sensitivity ceiling enforcement
- REST API (`/v1/gate`, `/v1/chat`, `/v1/doctor`)
- MCP server for tool integration
- CI pipeline (tests, lint, security scan)

**Framework alignment:** NIST AI RMF, OWASP Agentic Top 10 2026

---

## Phase 2: Output Governance Enhancement + Audit — ✅ Complete

**Focus:** Quantifiable governance + audit capability

**Delivered:**
- **GCS (Governance Compliance Score)** — Quantifiable governance metric with decision gates
- **Audit query interface** — `agentkb audit` CLI + REST endpoint
- **Evidence enforcement** — Block unsourced factual claims
- **Context integrity** — Session gate with drift detection
- **3-tier semantic detection** — Presidio + embeddings + optional LLM evaluator
- **Performance benchmarks** — NFR validation for latency/throughput

**Completed:** January 2026

---

## Phase 3: Access Control + Closed-Loop — 📋 Planned

**Focus:** Complete the AGENTS = PRINCIPALS thesis

**Planned deliverables:**
- **Content-layer RBAC** — Retrieval-time enforcement (not just output)
- **Claim type filtering** — Scope what agents can access by claim type
- **Closed-loop CLI** — `error-analyze`, `rule-propose`, `rule-apply`
- **Multi-tenant isolation** — KB-scoped roles + tenant context

**Target:** Q2 2026

---

## Phase 4: Enterprise + Ecosystem — 📋 Planned

**Focus:** Enterprise-ready deployment

**Planned deliverables:**
- **IdP integration** — OIDC/SAML → AgentKB roles
- **Tamper-proof audit** — Cryptographic signing for compliance
- **Python/JavaScript SDKs** — Native integration libraries
- **Audit dashboard** — Web UI for governance visibility
- **Meta-governance** — Rule lifecycle (retire obsolete rules)

**Target:** Q3-Q4 2026

---

## What We're Looking For

- **Design partners** — Organizations with AI governance challenges
- **Early adopters** — Developers building agentic applications
- **Technical feedback** — Framework gaps, UX friction, use case coverage

---

## Contact

**Jeffrey Wiley** — Founder  
📧 jeffreyjaywork@gmail.com

---

*© 2025-2026 Jeffrey Wiley. All rights reserved.*
