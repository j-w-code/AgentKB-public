# AgentKB Architecture Overview

> ⚠️ **PUBLIC DOCUMENT** — Conceptual overview only. Detailed schemas and specifications are proprietary.

---

## Design Philosophy

**Core premise:** AI agents are principals. They should be governed by the same least-privilege principles applied to human users in enterprise systems.

**Current state:** Most agentic systems rely on prompt-level trust ("don't access X") rather than enforcement-level control ("cannot access X").

**AgentKB approach:** Governance at the data layer, not the prompt layer.

---

## Layered Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    1. NAVIGATION LAYER                       │
│  Manifest-first traversal — explicit graph, no exploration   │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                    2. GOVERNANCE LAYER                       │
│  Policy enforcement — claim typing, sensitivity, constraints │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                    3. RETRIEVAL LAYER                        │
│  Policy-gated access — search, fetch, verify                 │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                    4. FEEDBACK LAYER                         │
│  Closed-loop governance — error → pattern → rule → codex     │
└─────────────────────────────────────────────────────────────┘
```

---

## Core Concepts

### Manifest-First Traversal

Agents navigate via explicit manifest links, not arbitrary file exploration.

**Why it matters:** Prevents agents from discovering content they shouldn't access. Navigation is controlled, not exploratory.

### Claim Typing

All data is classified by verifiability:
- Verifiable facts
- Computed/derived values
- Logical inferences
- Speculative hypotheses

**Why it matters:** Prevents agents from presenting speculation as fact. Output constraints ensure professional documents contain only verified claims.

### Policy Gates

Sensitivity levels enforced at retrieval time:
- Public (safe to disclose)
- Internal (restricted)
- PII (input-only by default)
- Secrets (never output)

**Why it matters:** Even if an agent is compromised or manipulated, it cannot output secrets. Enforcement, not trust.

### Closed-Loop Governance

```
Agent Error → Log → Pattern Detection → Rule Generation → Governance Update
```

**Why it matters:** The system learns from its own failures. Each error strengthens constraints. No model retraining required.

---

## Competitive Differentiation

| Capability | AgentKB | Vector DBs | RAG Frameworks |
|------------|---------|------------|----------------|
| Governance-first | ✓ | ✗ | ✗ |
| Claim typing | ✓ | ✗ | ✗ |
| Policy enforcement | ✓ | ✗ | Manual |
| Integrity verification | ✓ | ✗ | ✗ |
| Closed-loop learning | ✓ | ✗ | ✗ |

**Why competitors won't add this:** Vector databases optimize for retrieval speed; governance adds overhead they won't accept. RAG frameworks are pipelines; governance requires data-layer integration they're not architected for.

---

## Target Use Cases

1. **Enterprise RAG deployments** — Compliance-safe knowledge retrieval
2. **AI-assisted professional services** — Verifiable outputs for legal, finance, HR
3. **Multi-agent systems** — Scoped access for agent orchestration
4. **Personal knowledge management** — Individual professionals with AI workflows

---

## Current Implementation

A working MVP exists with:
- **Output Gate** — Blocks secrets and PII at output time
- **Access Gate** — Role-based content scoping at retrieval time (Phase 3)
- **RBAC** — Role-based access with sensitivity ceilings
- **Discovery scan** — PII/secret pattern detection across corpus
- **Closed-loop logging** — Blocked outputs feed error event schema
- **Replay harness** — Deterministic regression tests from historical failures

**Framework alignment:** Architecture independently derived, later validated against NIST AI RMF 1.0 (GOVERN, MAP, MEASURE), OWASP Agentic Security Initiative 2026 (8/10 categories), Gartner AI TRiSM (AI Governance + Runtime Enforcement), and Proofpoint AI Security research.

---

*For detailed specifications, contact: agentkb_jw@proton.me*

*© 2025-2026 J.W. All rights reserved.*
