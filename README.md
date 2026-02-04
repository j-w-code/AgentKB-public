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

## Framework Alignment & Compliance

AgentKB governance primitives align with:
- **NIST AI RMF 1.0** — Strong (3/4 functions)
- **OWASP Agentic Top 10 (2026)** — Strong (8/10 categories)
- **Therac-25 / Ariane 5 Benchmark** — Strong (4.7/5 categories)
- **Gartner AI TRiSM** — Strong (3/5 pyramid layers)
- **Proofpoint AI Security** — Strong (4/5 requirements)

### Compliance Support

| Standard | AgentKB Support |
|----------|----------------|
| **HIPAA** | PII detection, audit logging, sensitivity classification |
| **SOX** | Immutable audit trail, role-based access, governance versioning |
| **GDPR** | Data classification, output filtering, consent-aware scoping |

---

## Current Status

> ✅ **Phase 1-2.10.1 Public Release** (v0.5.3)

| Phase | Focus | Status |
|-------|-------|--------|
| **Phase 1** | Output Gate MVP | ✅ Complete |
| **Phase 2** | Audit + GCS + Evidence | ✅ Complete |
| **Phase 2.5-2.7** | Structural Enforcement (3-tier detection, Audit Bus) | ✅ Complete |
| **Phase 2.9** | Foundation Hardening (adversarial tests, locale patterns) | ✅ Complete |
| **Phase 2.9.5** | Four Operational Modes (gate independence) | ✅ Complete |
| **Phase 3** | Access Control (RBAC enforcement) | 🔒 Private |
| **Phase 4** | Enterprise (IdP, SDKs, Dashboard) | 🔒 Private |

> ⚠️ **Public Baseline:** This release is frozen at Phase 2.9.5. Phases 3+ continue in private development. See [ROADMAP.md](ROADMAP.md) for licensing details.

### What's New in v0.5.3

- **Audit Attribution Origin Field** (Phase 2.10): Every audit event now includes `context.origin` identifying the action source:
  - `human_cli` — Human via CLI commands
  - `agent_mcp` — Agent via MCP tools
  - `system_http` — System via REST API
  - Additional types: `human_http`, `agent_llm`, `system_cron`, `system_internal`
- **Session Correlation** (Phase 2.10.1): `origin.session_id` now populated for attribution correlation:
  - API: Uses existing session ID
  - CLI: Generates ephemeral session ID per invocation
  - MCP: Generates ephemeral session ID per tool call
- **MCP Audit Logging**: MCP tools now log to audit trail (was missing in prior releases)

**Test Coverage:** 671 tests passing • 67% code coverage • GCS 100

<details>
<summary>What was new in v0.5.2</summary>

- **Four Operational Modes**: FULL, SOLO-OG, SOLO-AG, ISLAND — gates can operate independently
- **GCS Matrix**: Unified compliance structure with two temporal dimensions
- **AccessGate primitive**: Input validation with nested ToolInvocationGate
- **Audit metrics API**: Structured metrics (blocks/day, rule coverage)
- **Locale-aware PII**: Regional pattern configurations
- **Adversarial test corpus**: Encoding attacks, prompt injection, tool exfiltration
- **E2E benchmark**: p95 latency 78.6ms (NFR: <3000ms)
</details>

**Available now:** CLI (16 commands), REST API (8 endpoints), MCP server (3 tools), multi-provider LLM support.

---

## Getting Started

> 🚀 **New to AgentKB?** Start with the [Quick Start Guide](QUICKSTART.md) — get running in ~10 minutes with zero Python experience required.
>
> 📖 Already comfortable with Python? Jump to the [User Guide](USER_GUIDE.md) for the full command reference.

### Installation

Download the wheel for your platform from [GitHub Releases](https://github.com/j-w-code/AgentKB-public/releases):

| Platform | Filename |
|----------|----------|
| Windows | `agentkb-*-cp312-cp312-win_amd64.whl` |
| Linux | `agentkb-*-cp312-cp312-manylinux*.whl` |
| macOS | `agentkb-*-cp312-cp312-macosx*.whl` |

Then install:

```bash
pip install agentkb-0.5.3-cp312-cp312-<your-platform>.whl
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
