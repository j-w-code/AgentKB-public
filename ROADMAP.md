# AgentKB Roadmap

> **Last updated:** March 2026

---

## Development Status

**Current state:** Phase 1–3 complete. Phase 4 open.
**Architecture:** Locked (Full System — access control + output governance + closed-loop)
**Development span:** December 31, 2025 → present (continuous)
**Sole author:** Jeffrey Wiley Jr.

---

## Phase 1: Output Gate MVP — ✅ Complete
**Completed:** January 2026

- `agentkb gate` — Output Gate for draft screening (PII, secrets, temporal claims)
- `agentkb scan` — PII/secret discovery across corpus
- `agentkb chat` — Gated LLM conversation (Ollama, Anthropic, OpenAI, xAI)
- `agentkb doctor` — Preflight diagnostics with risk summary
- RBAC sensitivity ceiling enforcement
- REST API + MCP server + CI pipeline

## Phase 2: Governance Enhancement + Audit — ✅ Complete
**Completed:** January 2026

- **Governance Compliance Score (GCS)** — Quantified metric with decision gates and active repair
- **Audit query interface** — CLI + REST endpoint for structured governance telemetry
- **Evidence enforcement** — Block unsourced factual claims
- **Context integrity** — Session gate with SHA-256 drift detection
- **Performance benchmarks** — All NFRs validated (Output Gate p95: <1ms, Init Gate: <10ms)

## Phase 2.5–2.9.5: Structural Enforcement — ✅ Complete
**Completed:** January 2026

- **3-tier semantic detection** — Pattern matching + trained embeddings (22 classes, >90% recall) + optional LLM evaluator
- **Audit Bus** — Event coordination for gate-to-gate communication
- **Four Operational Modes** — FULL, SOLO-OG, SOLO-AG, ISLAND (true gate independence)
- **Access Gate primitive** — Input validation with nested Tool Invocation Gate
- **Adversarial test corpus** — Encoding attacks, prompt injection, tool exfiltration scenarios
- **Locale-aware PII** — Regional pattern configurations
- **Degraded mode handling** — Governed failover (availability with visibility)

## Phase 3: Access Control + Closed-Loop + Enterprise Auth — ✅ Complete
**Completed:** February 2026 (18 items across 4 execution tracks)

**Track A — Enterprise Auth (Critical Path):**
- Explicit authority hierarchy (Codex > Admin > Human > Agent)
- Role assignment authority with validation model
- Agent authentication (API-key with caller identity declaration)
- Agent registry (enrollment, status tracking, decommission)
- Agent lifecycle management (provisioning → active → suspended → decommissioning → decommissioned)

**Track B — Access Control (Gate Parity):**
- Content-layer RBAC with retrieval-time enforcement
- Claim type filtering (scope by verifiability level)
- Tool invocation gate with payload scanning, destination governance, and skill documentation pre-scan
- MCP caller verification and provenance contract
- Output gate encoding normalization (base64, ROT13, unicode confusable folding)

**Track C — Multi-Tenant + Provider:**
- Multi-tenant isolation (KB-scoped roles with organizational boundaries)
- Provider abstraction v2 (consistent governance across Ollama, Anthropic, OpenAI, xAI, local models)
- Governance indicator API (real-time governance posture for consuming systems)

**Track D — Closed-Loop:**
- `agentkb error-analyze` — Pattern abstraction from governance failure audit trails
- `agentkb rule-propose` — Human approval workflow (propose/approve/reject)
- `agentkb rule-apply` — Deterministic governance mutation with provenance tracking
- Full closed-loop parity across CLI, REST API, and MCP

**Additional:**
- Session termination policy (configurable idle timeout, max duration, activity-based enforcement)

---

## Phase 4: Enterprise + Meta-Governance + SDK — 🔓 Open
**Target:** Q3–Q4 2026
**Theme:** Enterprise-ready with full rule lifecycle and SDK ecosystem

### Wave 4.1 — Enterprise Auth
- OAuth/OIDC federated identity provider integration (Okta, Auth0, Azure AD, Keycloak)
- Just-in-time privilege elevation with human approval and automatic revocation
- Tamper-proof audit with cryptographic signing (compliance: SOX, HIPAA, 7yr retention)

### Wave 4.2 — Meta-Governance
- Governance health analysis CLI
- Decision trace export for compliance/procurement evidence
- ISO 42001 control applicability register
- Operator sequence guards for high-impact governance mutations
- Rule retirement and consolidation with human approval
- Human approval scalability (risk-tiered routing, backlog control)

### Wave 4.3 — SDK & Integration
- Python and JavaScript SDKs
- Inter-agent security primitives (signed envelopes, delegation chains)
- Cross-surface policy parity verification (API/CLI/MCP/chat)
- Zero-trust privileged-mutation cutover (principal-bound authorization)

### Wave 4.4 — Context Hardening
- User-defined scope locks (generalized architecture lock)
- Hash-based context fingerprint with drift detection
- Memory trust classification by source provenance

### Wave 4.5 — UX & Operations
- Usefulness primitive (governance-compliant AND helpful — shadow IT prevention)
- Exception workflow UX for blocked flows
- Progressive rule expansion from boundary encounters
- AI impact assessment workflow (ISO 42001 aligned)
- Verification triage for scarce human expert capacity
- RBAC resource allocation (context budget, persistence tier, audit depth by role)

### Wave 4.6 — CMMC Compliance
- Insider threat awareness documentation
- Supply chain risk plan and dependency governance
- Media disposal procedures for governed data lifecycle

---

## Phase 5: Advanced Security Layer — Planned
**Theme:** Detection, behavioral analytics, and security operations built on the governance substrate

- **Foundation:** Compliance reporting, data flow documentation, credential rotation, air-gapped deployment, access alerting
- **Detection:** Context source verification, agent behavioral baselines, verification independence guardrails
- **Analytics:** Behavioral anomaly detection, agent social engineering detection
- **Security Operations:** Agent honeypots, incident response playbooks, security alert integration
- **Research:** Collapse navigation engine (geometric retrieval), intent-aware authorization investigation

Design principle: **Governance first, security layer second.** Phases 1–4 establish the governance substrate. Phase 5 builds detection and response capabilities on top of that foundation.

---

## Phase 6: Productization — Planned
**Theme:** Enterprise-deployable product experience

- **Experience surface:** Page-based operator UX, embedded governance indicators, multi-surface parity
- **Governance operations:** Decision trace explorer, human approval cockpit, exception/remediation workflows
- **Deployment packaging:** Guided onboarding (IdP, tenant bootstrap, policy seeding), operational pack (alerts, dashboards, SLAs), procurement evidence bundles

Hard constraint: productization cannot relax fail-closed synchronous decision semantics.

---

## Framework Alignment

| Framework | Alignment |
|-----------|----------|
| NIST AI RMF 1.0 | Strong (GOVERN, MAP, MEASURE) |
| OWASP Agentic Security Initiative 2026 | Strong (8.5/10 categories) |
| ISO/IEC 42001:2023 | Strong (AI management system governance alignment) |
| Gartner AI TRiSM | Strong (AI Governance + Runtime Enforcement) |
| DoD CMMC 2.13 | Partial (~65% applicable; CM, CA, AU strong) |
| Therac-25 / Ariane 5 Benchmark | Strong (4.7/5 failure mode categories) |
| Proofpoint AI Security Research | Strong (structural defense vs. agentic threats) |
| Koi Security ClawHavoc | Strong (10/12 supply chain attack vectors) |

---

## Licensing

| Access Level | What You Get |
|--------------|-------------|
| **Public (this repo)** | Architecture docs, public reference, evaluation use |
| **Commercial License** | Runtime access, production rights, priority support |

---

## Contact

**Jeffrey Wiley Jr.** — Founder
📧 agentkb_jw@proton.me

- Commercial licensing inquiries
- Design partner opportunities
- Technical feedback

---

*© 2025-2026 Jeffrey Wiley Jr. All rights reserved.*
