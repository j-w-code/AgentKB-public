# AgentKB

**Governance enforcement for agentic workflows.**

AgentKB is built for teams that need runtime boundaries across **access/input/tool/output**, deterministic policy decisions, and audit-grade traceability.

---

## One-line Positioning

I’m building AgentKB: governance enforcement for agentic workflows (runtime boundaries across access/input/tool/output, deterministic policy decisions, and audit-grade traceability). We’ve focused on structural enforcement over prompt trust.

---

## Why AgentKB

Most agentic systems can authenticate users and call models, but still struggle to **enforce** governance at runtime:

- What an agent is allowed to read
- What an agent is allowed to disclose
- What an agent is allowed to invoke
- How every decision is explained and auditable

AgentKB treats agents as principals and enforces policy through deterministic gates rather than relying on behavioral compliance.

---

## Runtime Boundary Model

```text
┌───────────────────────────────────────────────────────────┐
│                    GOVERNANCE POLICY                      │
│        (roles, constraints, decision contracts)           │
└────────────────────┬──────────────────────────────────────┘
                     │
                     ▼
┌───────────────────────────────────────────────────────────┐
│ ACCESS / INPUT BOUNDARY                                  │
│ • role/scope constraints                                  │
│ • sensitivity-aware filtering                             │
└────────────────────┬──────────────────────────────────────┘
                     │
                     ▼
                   AGENT
                     │
                     ▼
┌───────────────────────────────────────────────────────────┐
│ OUTPUT BOUNDARY                                           │
│ • disclosure controls                                     │
│ • PII/secret redaction/block semantics                    │
└────────────────────┬──────────────────────────────────────┘
                     │
                     ▼
┌───────────────────────────────────────────────────────────┐
│ TOOL INVOCATION BOUNDARY                                  │
│ • allow/deny by role + context                            │
│ • outbound action governance                              │
└────────────────────┬──────────────────────────────────────┘
                     │
                     ▼
┌───────────────────────────────────────────────────────────┐
│ AUDIT & ASSURANCE                                         │
│ • decision lineage                                        │
│ • reason codes + replay-ready traces                      │
└───────────────────────────────────────────────────────────┘
```

---

## What the Public Repository Includes

Public release includes the high-level architecture and governance primitives for:

- Output governance
- Access/input governance primitives
- Audit and compliance telemetry
- Operational hardening patterns

For the public baseline, see:

- [`ARCHITECTURE.md`](ARCHITECTURE.md)
- [`ROADMAP.md`](ROADMAP.md)
- [`API_REFERENCE.md`](API_REFERENCE.md)
- [`MCP_INTEGRATION.md`](MCP_INTEGRATION.md)
- [`USER_GUIDE.md`](USER_GUIDE.md)

---

## Current Status (Public View)

### Public baseline
- **Release line:** v0.5.x
- **Coverage:** Phase 1 → Phase 2.9.5 public baseline
- **Repository intent:** source-available evaluation and high-level technical reference

### Private ongoing development
Phases 3+ continue in private development, focused on enterprise readiness and broader ecosystem integration. Public docs are intentionally high level and non-proprietary.

For public roadmap boundaries, see [`ROADMAP.md`](ROADMAP.md).

---

## Use Cases

- Enterprise AI assistants with governance requirements
- Multi-tenant agentic systems with scoped data access
- Compliance-heavy environments requiring decision traceability
- Teams needing structural controls beyond prompt-level guardrails

---

## Framework Alignment (High-Level)

AgentKB governance primitives are designed to align with widely used enterprise governance and security frameworks, including:

- NIST AI RMF 1.0
- OWASP Agentic Security Initiative (2026)
- AI TRiSM-style governance and runtime control models

Detailed mapping is maintained in private governance artifacts and selectively exposed in public form where appropriate.

---

## Getting Started

If you want a fast orientation:

1. Read [`ARCHITECTURE.md`](ARCHITECTURE.md)
2. Review [`DEMO_METHODOLOGY.md`](DEMO_METHODOLOGY.md)
3. Use [`QUICKSTART.md`](QUICKSTART.md) for local setup

Wheel artifacts are published via GitHub Releases:

`https://github.com/j-w-code/AgentKB-public/releases`

---

## License

Source-available. See [`LICENSE`](LICENSE) and [`NOTICE`](NOTICE).

---

## Contact

**J.W.** — Founder  
📧 `agentkb_jw@proton.me`

- Technical feedback
- Design partner conversations
- Commercial licensing inquiries

---

*Built for a world where AI agents are principals, not just tools.*
