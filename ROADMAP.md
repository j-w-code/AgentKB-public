# AgentKB Roadmap

> **PUBLIC DOCUMENT** — High-level phases. Detailed specs in private development repo.

---

## Current Status

**Stage:** Pre-seed / Active Development  
**Next Release:** v0.2.0 (Q1 2026) — Source-available code drop

---

## Phase 1: Output Gate MVP — ✅ Complete (Internal)

**Delivered (not yet public):**
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

## Phase 2: Output Governance Enhancement + Audit — 🔄 In Progress

**Focus:** Quantifiable governance + audit capability

**Planned deliverables:**
- **GCS (Governance Compliance Score)** — Quantifiable governance metric
- **Audit query interface** — `agentkb audit` CLI + REST endpoint
- **Evidence enforcement** — Block unsourced factual claims
- **Context integrity** — Drift detection for long sessions

**v0.2.0 release includes:**
- Full source code (Python package)
- CLI with all Phase 1-2 commands
- REST API server
- MCP server
- Documentation + quickstart guide

**Target:** Q1 2026

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
