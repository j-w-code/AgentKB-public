# AgentKB

**Governance-first knowledge infrastructure for AI agents.**

---

## The Core Thesis

> **AGENTS = PRINCIPALS**

AI agents operating on enterprise data require the same governance controls as human employees:
- **Access Control** — Who can see what
- **Output Control** — What can be said externally
- **Audit Trail** — Who did what, when

Most AI security focuses only on access. AgentKB addresses all three.

---

## What AgentKB Does

AgentKB provides governance infrastructure for AI agents:

1. **Access Control** — Scope what agents can read by role and sensitivity [Phase 3]
2. **Output Gate** — Block PII, secrets, and unverifiable claims before disclosure [Available]
3. **Audit Logging** — Every gate decision logged for compliance [Available]
4. **Claim Validation** — Enforce evidence requirements for factual claims [Available]

---

## Architecture: Two-Gate Model

```
┌──────────────────────────────────────────────────────────┐
│                    KNOWLEDGE BASE                         │
│         (Content with sensitivity markers)                │
└────────────────────┬─────────────────────────────────────┘
                     │
                     ▼
┌──────────────────────────────────────────────────────────┐
│                 ACCESS GATE (Phase 3)                     │
│   • Filters content BEFORE agent sees it                 │
│   • RBAC: Role → Content scoping                         │
│   • Prevents sensitive data reaching LLM provider        │
└────────────────────┬─────────────────────────────────────┘
                     │
                     ▼
                   AGENT
                     │
                     ▼
┌──────────────────────────────────────────────────────────┐
│                 OUTPUT GATE (Available)                   │
│   • Scans agent outputs before disclosure                │
│   • PII/secret detection, claim validation               │
│   • Governance Compliance Score (GCS) enforcement        │
└────────────────────┬─────────────────────────────────────┘
                     │
                     ▼
                  OUTPUT
```

### Two-Gate Protection

**Input Side (Phase 3):** Access control filters content *before* it reaches the agent/LLM provider. Agents only see what their role permits.

**Output Side (Available):** Output gate validates agent responses before disclosure. Blocks PII, secrets, and unverifiable claims.

Together: Defense-in-depth for enterprise AI.

---

## Use Cases

- **Enterprise AI Assistants** — Prevent confidential data leakage
- **Multi-tenant Systems** — Scope agent knowledge by customer/role
- **Compliance Environments** — Audit what agents accessed and disclosed
- **Agentic Applications** — Governance middleware for LLM pipelines

---

## Framework Alignment

AgentKB governance primitives align with:
- **NIST AI RMF 1.0** — GOVERN, MAP, MEASURE functions
- **OWASP Agentic Top 10 (2026)** — 8/10 categories covered
- **Real-World Systems Failure Tests** — 4.7/5 (Therac-25, Ariane 5 coverage)

---

## Current Status

> ✅ **Phase 1-2 Public Release** (v0.4.10)

| Phase | Focus | Status |
|-------|-------|--------|
| **Phase 1** | Output Gate MVP | ✅ Complete |
| **Phase 2** | Audit + GCS + Evidence | ✅ Complete |
| **Phase 3** | Access Control (RBAC enforcement) | 📋 Planned |
| **Phase 4** | Enterprise (IdP, SDKs, Dashboard) | 📋 Planned |

**Available now:** CLI, REST API, MCP server, multi-provider LLM support.

---

## Getting Started

### Installation

Download the wheel for your platform from [GitHub Releases](https://github.com/j-w-code/AgentKB-public/releases):

| Platform | Filename |
|----------|----------|
| Windows | `agentkb-*-cp312-cp312-win_amd64.whl` |
| Linux | `agentkb-*-cp312-cp312-manylinux*.whl` |
| macOS | `agentkb-*-cp312-cp312-macosx*.whl` |

Then install:

```bash
pip install agentkb-0.4.10-cp312-cp312-<your-platform>.whl
```

### Quick Start

```bash
agentkb init        # Initialize workspace
agentkb doctor      # Verify setup
agentkb gate --text "Hello world"  # Test output gate
```

---

## License

**Source-Available.** See [LICENSE](LICENSE) for details.

- ✅ View, modify, run for development/testing
- ✅ Evaluate before commitment
- ❌ Production use requires commercial license

---

## Contact

**J.W.** — Founder  
📧 agentkb_jw@proton.me  
🐦 [@HSThurston](https://x.com/HSThurston) (Hermes Thurston — Creative Collaborator)

- Commercial licensing inquiries
- Design partner opportunities
- Technical feedback

---

*Built for a world where AI agents are principals, not just tools.*
