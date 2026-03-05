# AgentKB Architecture Overview

> **PUBLIC DOCUMENT** — Conceptual architecture overview. Detailed schemas and runtime specifications are proprietary.

---

## Design Philosophy

**Core premise:** AI agents are principals. They should be governed by the same least-privilege principles applied to human users in enterprise systems.

**Current state:** Most agentic systems rely on prompt-level trust ("don't access X") rather than enforcement-level control ("cannot access X").

**AgentKB approach:** Governance at the data layer, not the prompt layer. Structural enforcement over behavioral compliance.

**Key distinction:** Governance ≠ Safety. Governance is structural and persistent (what can be accessed, disclosed, invoked). Safety is subjective and ethical (bias, harmful content). AgentKB provides governance; safety remains the concern of model providers. A properly implemented governance system actually *increases* what agents can permissibly do.

---

## Full System Architecture

```text
┌─────────────────────────────────────────────────────────────┐
│                    1. NAVIGATION LAYER                       │
│  Manifest-first traversal — explicit graph, no exploration   │
└──────────────────────────────┬──────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────┐
│                    2. GOVERNANCE LAYER                       │
│  Authority hierarchy · RBAC · claim typing · sensitivity     │
│  Codex > Enterprise Admin > Human Employee > Agent           │
└──────────────────────────────┬──────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────┐
│                    3. GATE LAYER                             │
│  Access Gate · Output Gate · Tool Invocation Gate            │
│  Independent operation · four modes · fail-closed default    │
└──────────────────────────────┬──────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────┐
│                    4. RETRIEVAL LAYER                        │
│  Policy-gated access — search, fetch, verify                 │
│  Content-layer RBAC · sensitivity ceiling · claim filtering  │
└──────────────────────────────┬──────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────┐
│                    5. AUDIT & FEEDBACK LAYER                 │
│  Audit Bus · GCS · decision lineage · reason codes           │
│  Closed-loop: error → pattern → rule → human approval        │
└─────────────────────────────────────────────────────────────┘
```

---

## Core Concepts

### Authority Hierarchy

Explicit 4-level authority chain: **Codex > Enterprise Admin > Human Employee > Agent.**

The codex (governance policy) is the universal floor. Enterprise admins configure organizational constraints. Human employees operate within those constraints. Agents inherit the most restrictive applicable set.

### Manifest-First Traversal

Agents navigate via explicit manifest links, not arbitrary file exploration.

**Why it matters:** Prevents agents from discovering content they shouldn't access. Navigation is controlled, not exploratory.

### Claim Typing

All data is classified by verifiability:
- **FACT** — Verifiable, directly stated
- **DERIVED** — Computed from facts (math explicit)
- **INFERENCE** — Logical conclusion from facts/derived
- **HYPOTHESIS** — Forward-looking/speculative
- **OPINION** — Subjective framing

Output constraints are scoped by claim type. Agents cannot present inference as fact or hypothesis as established conclusion.

### Sensitivity Enforcement

Four sensitivity levels enforced at both retrieval and output time:
- **Public** — Allowed
- **Internal** — Restricted (audit optional)
- **PII** — Input-only by default (forbidden in output)
- **Secret** — Never output, under any circumstances

Even if an agent is compromised or manipulated, it cannot output secrets. This is structural enforcement, not trust.

### Three Governance Gates

**Access Gate** — Content-layer RBAC with retrieval-time filtering by path, sensitivity, and claim type. Prompt injection defense. Rate limiting.

**Output Gate** — 3-tier detection architecture:
- Tier 1: Pattern matching (literal, ~1-5ms)
- Tier 2: Trained semantic embeddings (22 reference classes, ~10-50ms)
- Tier 3: LLM evaluator (optional, novel patterns only, ~100-500ms)

Encoding normalization pre-pass (base64, ROT13, unicode confusable folding) before detection.

**Tool Invocation Gate** — Pre/post validation on agent tool calls. Payload content scanning, destination allowlists, and skill documentation pre-scan for supply chain attack patterns.

All three gates operate independently. Four operational modes (FULL, SOLO-OG, SOLO-AG, ISLAND) ensure governance continues even when individual gates are unhealthy.

### Agent Identity

Agents are first-class principals with:
- **Authentication** — API-key with caller identity declaration
- **Registry** — Central enrollment, status tracking, decommission
- **Lifecycle states** — provisioning → active → suspended → decommissioning → decommissioned
- **Role assignment** — Validated authority model for who can assign which roles

### Closed-Loop Governance

```text
Agent Error → Audit Log → Pattern Analysis → Rule Proposal → Human Approval → Governance Update
```

The system learns from its own failures. Each error strengthens constraints through a human-approved pipeline. No model retraining required. All rule mutations require explicit human confirmation regardless of confidence level.

### Governance Compliance Score (GCS)

Quantified governance metric with asymptotic scoring model. Decision gates (architectural_decision, capability_removal, rule_proposal, content_mutation) each require minimum GCS thresholds. Active repair workflows allow recovery from violations.

### Multi-Tenant Isolation

KB-scoped roles with organizational boundary enforcement. The codex remains the universal governance floor; tenant context adds organizational constraints on top.

---

## Competitive Differentiation

| Capability | AgentKB | Vector DBs | RAG Frameworks | IAM Platforms |
|------------|---------|------------|----------------|---------------|
| Governance-first architecture | ✓ | ✗ | ✗ | Partial |
| Claim typing + output control | ✓ | ✗ | ✗ | ✗ |
| Retrieval-time policy enforcement | ✓ | ✗ | Manual | ✗ |
| Tool invocation payload scanning | ✓ | ✗ | ✗ | Access only |
| Closed-loop governance learning | ✓ | ✗ | ✗ | ✗ |
| Agent lifecycle management | ✓ | ✗ | ✗ | ✓ |
| Temporal integrity controls | ✓ | ✗ | ✗ | ✗ |
| Independent gate operation | ✓ | N/A | N/A | N/A |

Vector databases optimize for retrieval speed; governance adds overhead they won't accept. RAG frameworks are pipelines; governance requires data-layer integration they're not architected for. IAM platforms (CyberArk, etc.) handle identity and access but not content-level output governance, claim typing, or closed-loop learning.

---

## Target Use Cases

1. **Enterprise RAG deployments** — Compliance-safe knowledge retrieval with audit trails
2. **AI-assisted professional services** — Verifiable outputs for legal, finance, HR
3. **Multi-agent systems** — Scoped access with tool invocation governance
4. **MCP-based agentic platforms** — Policy enforcement at the integration layer
5. **Regulated environments** — SOX, HIPAA, GDPR, CMMC compliance-ready governance
6. **Multi-tenant deployments** — Organizational boundary enforcement with shared infrastructure

---

## Current Implementation

Phases 1–3 are complete. The runtime includes:

- **Three governance gates** — Access, Output, Tool Invocation (all with full parity across CLI/API/MCP)
- **Agent identity foundation** — Authentication, registry, lifecycle, authority hierarchy
- **Closed-loop pipeline** — Error analysis → rule proposal → human approval → governance mutation
- **Multi-tenant + multi-provider** — Tenant isolation, consistent governance across LLM providers
- **Detection & hardening** — 3-tier detection, encoding normalization, adversarial corpus, session termination
- **Audit infrastructure** — Audit Bus, structured metrics, session fingerprinting, MCP caller verification
- **Kubernetes deployment** — Transport, session store, and scaling configuration

Phase 4 (enterprise hardening, meta-governance, SDKs) is open. See [`ROADMAP.md`](ROADMAP.md).

### Framework Alignment

Architecture independently derived, then systematically validated against:
- NIST AI RMF 1.0 (GOVERN, MAP, MEASURE)
- OWASP Agentic Security Initiative 2026 (8.5/10 categories)
- Gartner AI TRiSM (AI Governance + Runtime Enforcement)
- DoD CMMC 2.13 (~65% applicable domains)
- Therac-25 / Ariane 5 failure mode benchmark (4.7/5 categories)
- Proofpoint AI Security Research (structural defense vs. agentic threats)
- Koi Security ClawHavoc (10/12 supply chain attack vectors)

---

*For detailed specifications, contact: agentkb_jw@proton.me*

*© 2025-2026 Jeffrey Wiley Jr. All rights reserved.*
