# AgentKB

**Governance enforcement for agentic AI workflows.**

Runtime boundaries across access, input, tool invocation, and output. Deterministic policy decisions. Audit-grade traceability. Structural enforcement over prompt trust.

---

## Core Thesis: Agents Are Principals

Current agentic systems rely on prompt-level trust ("don't access X") rather than enforcement-level control ("cannot access X"). Only humans are held accountable for agent actions, yet agents operate without the same governance structures that bind human employees.

AgentKB treats AI agents as principals — governed by least-privilege enforcement, not behavioral compliance:

- **Access Control** — Agents have roles with defined read/write permissions, enforced at retrieval time
- **Output Control** — Agent outputs are gated by sensitivity and claim type before delivery
- **Tool Control** — Agent tool invocations are governed by payload inspection and destination policy
- **Audit Trail** — Every decision is logged with identity, role, reason code, and lineage
- **Closed-Loop** — Agent errors systematically strengthen governance over time

---

## Runtime Boundary Model

```text
┌───────────────────────────────────────────────────────────┐
│                    GOVERNANCE POLICY                      │
│   authority hierarchy · roles · constraints · contracts   │
└────────────────────┬──────────────────────────────────────┘
                     │
                     ▼
┌───────────────────────────────────────────────────────────┐
│ ACCESS / INPUT GATE                                      │
│ • content-layer RBAC (path, sensitivity, claim type)     │
│ • retrieval-time filtering                               │
│ • prompt injection defense                               │
└────────────────────┬──────────────────────────────────────┘
                     │
                     ▼
                   AGENT
                     │
                     ▼
┌───────────────────────────────────────────────────────────┐
│ OUTPUT GATE                                              │
│ • 3-tier detection (literal + semantic + LLM evaluator)  │
│ • PII/secret block with encoding normalization           │
│ • claim evidence requirements                            │
└────────────────────┬──────────────────────────────────────┘
                     │
                     ▼
┌───────────────────────────────────────────────────────────┐
│ TOOL INVOCATION GATE                                     │
│ • payload content scanning                               │
│ • destination allowlists                                 │
│ • skill documentation pre-scan                           │
└────────────────────┬──────────────────────────────────────┘
                     │
                     ▼
┌───────────────────────────────────────────────────────────┐
│ AUDIT & CLOSED-LOOP                                      │
│ • decision lineage + reason codes                        │
│ • error → pattern → rule proposal → human approval       │
│ • governance strengthens from every failure              │
└───────────────────────────────────────────────────────────┘
```

All gates operate independently. When one gate is unhealthy, the remaining gates continue enforcing governance autonomously (four operational modes: FULL, SOLO-OG, SOLO-AG, ISLAND).

---

## What's Built

Phases 1 through 3 are **complete**. The runtime includes:

### Governance Gates
- **Access Gate** — Content-layer RBAC with retrieval-time enforcement, sensitivity ceiling, claim type filtering
- **Output Gate** — 3-tier detection (pattern matching, trained semantic embeddings, optional LLM evaluator), encoding normalization (base64, ROT13, unicode confusable folding)
- **Tool Invocation Gate** — Pre/post validation on agent tool calls with payload scanning, destination governance, and skill documentation pre-scan
- **Governance Compliance Score (GCS)** — Quantified governance metric with decision-gate thresholds and active repair workflows

### Agent Identity & Lifecycle
- **Authority Hierarchy** — Explicit 4-level authority chain (Codex > Admin > Human > Agent)
- **Agent Authentication** — API-key authentication with caller identity declaration
- **Agent Registry** — Enrollment, status tracking, and decommission for known agents
- **Lifecycle Management** — Full state machine (provisioning → active → suspended → decommissioning → decommissioned) with audited transitions
- **Role Assignment Authority** — Validated model for who can assign which roles to agents

### Closed-Loop Governance
- **Error Analysis** — Pattern abstraction from governance failure audit trails
- **Rule Proposal** — Human approval workflow for governance rule changes (propose/approve/reject)
- **Rule Application** — Deterministic governance mutation with provenance tracking
- Full pipeline parity across CLI, REST API, and MCP interfaces

### Multi-Tenant & Provider Abstraction
- **Tenant Isolation** — KB-scoped roles with organizational boundary enforcement
- **Provider Abstraction** — Consistent governance across Ollama, Anthropic, OpenAI, xAI, and local models
- **Governance Indicator API** — Real-time governance posture reporting for consuming systems

### Detection & Hardening
- **3-Tier Semantic Detection** — Pre-trained across 22 reference classes with >90% recall
- **Encoding Normalization** — Deterministic pre-pass before sensitivity checks
- **Adversarial Test Corpus** — Encoding attacks, prompt injection, tool exfiltration scenarios
- **Session Termination Policy** — Configurable idle timeout, max duration, and activity-based enforcement

### Audit & Compliance
- **Audit Bus** — Event coordination layer for gate-to-gate communication
- **Structured Audit Metrics** — Queryable governance telemetry (blocks, rule coverage, decision distributions)
- **Session Gate** — SHA-256 fingerprinting with drift detection
- **MCP Integration** — Caller verification and provenance contract across the MCP tool surface
- **Four Operational Modes** — Governed degradation (availability with visibility over strict unavailability)

### Interfaces
- **CLI** — 15+ governance commands
- **REST API** — Full endpoint coverage for programmatic integration
- **MCP Server** — Tool integration for agentic platforms
- **Kubernetes** — Deployment manifests with transport, session store, and scaling configuration

---

## Development Status

| Phase | Status | Theme |
|-------|--------|-------|
| Phase 1 | **Complete** | Output Gate MVP — CLI, REST API, MCP, multi-provider LLM |
| Phase 2 | **Complete** | GCS, audit foundation, evidence enforcement, context integrity |
| Phase 2.5–2.9.5 | **Complete** | 3-tier detection, Audit Bus, four operational modes, adversarial corpus |
| Phase 3 | **Complete** | Access control, closed-loop pipeline, agent identity, multi-tenant, tool gate, enterprise auth foundation |
| Phase 4 | **Open** | Enterprise hardening, meta-governance, SDK ecosystem |
| Phase 5 | Planned | Advanced security layer (detection, behavioral analytics, security operations) |
| Phase 6 | Planned | Productization (enterprise UX, operational packaging, deployment tooling) |

Development began December 31, 2025. Continuous development through present. Sole author: Jeffrey Wiley Jr.

---

## What's Next (Phase 4+)

Phase 4 is organized into execution waves:

**Enterprise Auth** — OAuth/OIDC federated identity provider integration, just-in-time privilege elevation with human approval, tamper-proof audit with cryptographic signing

**Meta-Governance** — Rule lifecycle management (retire obsolete rules, consolidate overlapping rules), governance health analysis, operator safety interlocks for high-impact mutations, decision trace export for compliance evidence

**SDK & Integration** — Python and JavaScript SDKs, inter-agent security primitives, cross-surface policy parity verification, zero-trust privileged-mutation cutover

**Context Hardening** — User-defined scope locks, hash-based session drift detection, memory trust classification by source provenance

**UX & Operations** — Usefulness optimization (governance-compliant AND helpful), exception workflow UX, progressive rule expansion, compliance framework alignment tooling (ISO 42001, CMMC)

**Phase 5** builds an advanced security layer on top of the governance substrate: behavioral baselines, anomaly detection, adversarial prompt detection, agent incident response playbooks, air-gapped deployment support.

**Phase 6** converts the runtime into an enterprise-deployable product: page-based operator UX, governance operations cockpit, deployment packaging, and procurement evidence bundles.

---

## Framework Alignment

AgentKB governance primitives have been validated against:

- **NIST AI RMF 1.0** — Strong (GOVERN, MAP, MEASURE)
- **OWASP Agentic Security Initiative 2026** — Strong (8.5/10 categories)
- **Gartner AI TRiSM** — Strong (AI Governance + Runtime Enforcement)
- **DoD CMMC 2.13** — Partial (~65% applicable domains; CM, CA, AU strong)
- **Therac-25 / Ariane 5 Benchmark** — Strong (4.7/5 failure mode categories)
- **Proofpoint AI Security Research** — Strong (structural defense vs. agentic threats)
- **Koi Security ClawHavoc** — Strong (10/12 supply chain attack vectors)

Architecture was independently derived, then systematically validated against these frameworks.

---

## Use Cases

- Enterprise AI assistants with governance and compliance requirements
- Multi-tenant agentic systems with scoped data access and tenant isolation
- Compliance-heavy environments requiring deterministic decision traceability (SOX, HIPAA, GDPR)
- Teams deploying agents that invoke external tools and need payload-level governance
- Organizations adopting MCP-based agentic platforms that need policy enforcement at the integration layer
- Any environment where structural enforcement is required beyond prompt-level guardrails

---

## Getting Started

For a fast orientation:

1. Read [`ARCHITECTURE.md`](ARCHITECTURE.md)
2. Review [`DEMO_METHODOLOGY.md`](DEMO_METHODOLOGY.md)
3. Use [`QUICKSTART.md`](QUICKSTART.md) for local setup

Wheel artifacts are published via GitHub Releases:

`https://github.com/j-w-code/AgentKB-public/releases`

Additional public documentation:

- [`API_REFERENCE.md`](API_REFERENCE.md)
- [`MCP_INTEGRATION.md`](MCP_INTEGRATION.md)
- [`USER_GUIDE.md`](USER_GUIDE.md)
- [`ROADMAP.md`](ROADMAP.md)

---

## Provenance

See [`PROVENANCE.md`](PROVENANCE.md) for timestamped authorship attestation, development timeline, commit boundary hashes, and SHA-256 integrity hashes of private evidence artifacts.

---

## License

Source-available. See [`LICENSE`](LICENSE) and [`NOTICE`](NOTICE).

---

## Contact

**Jeffrey Wiley Jr.** — Founder
📧 `agentkb_jw@proton.me`

- Technical feedback
- Design partner conversations
- Commercial licensing inquiries

---

*Built for a world where AI agents are principals, not just tools.*
