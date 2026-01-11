# AgentKB Roadmap

> ⚠️ **PUBLIC DOCUMENT** — High-level phases only.

---

## Current Status

**Stage:** Pre-seed / MVP

Working CLI implementation:
- Output Gate (secrets/PII blocking)
- RBAC with role-based sensitivity ceilings
- Discovery scan for PII/secret patterns
- Closed-loop error logging
- REST API server (FastAPI)
- MCP server for tool integration

**Validation:** NIST AI RMF 1.0, OWASP Agentic Top 10 2026

---

## Phase 1: CLI + Local (MVP) — ✅ Complete

**Delivered:**
- `agentkb gate` — Output Gate for draft screening
- `agentkb scan` — PII/secret discovery
- `agentkb chat` — Gated LLM conversation
- `agentkb doctor` — Preflight diagnostics
- RBAC with four default roles
- REST API + MCP server
- CI pipeline (tests, lint, security scan)

**Target:** Developers and AI-first professionals

---

## Phase 2: API + Integrations

**Focus:** Programmatic access and ecosystem integration

- REST API
- Python and JavaScript SDKs
- MCP protocol support (Anthropic tool use)
- LangChain / LlamaIndex adapters

**Target:** Development teams building agentic applications

---

## Phase 3: Hosted + Dashboard

**Focus:** Enterprise deployment and collaboration

- Cloud deployment option
- Web dashboard for governance configuration
- Team collaboration features
- Enterprise governance templates
- Compliance reporting

**Target:** Enterprise AI deployments

---

## What We're Looking For

- **Funding:** Pre-seed to build MVP and reach first customers
- **Design partners:** Organizations with AI governance challenges
- **Technical feedback:** Developers building agentic systems

---

## Contact

**Jeffrey Wiley**  
📧 jeffreyjaywork@gmail.com

---

*© 2025-2026 Jeffrey Wiley. All rights reserved.*
