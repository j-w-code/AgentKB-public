# AgentKB

**Governance-first knowledge management for AI agent systems.**

---

## The Core Thesis

> **AGENTS = PRINCIPALS**

AI agents operating on enterprise data require the same governance controls as human employees:
- **Access Control** — Who can see what
- **Output Control** — What can be said externally

Most AI security focuses only on the first. AgentKB addresses both.

---

## What AgentKB Does

AgentKB provides a structured knowledge base layer with built-in governance primitives:

1. **Sensitivity Classification** — Content marked by disclosure level
2. **Role-Based Access** — Agents receive scoped context based on assigned roles
3. **Output Governance** — Runtime scanning prevents unauthorized disclosure
4. **Audit Trail** — Every access and output decision logged

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────┐
│                    KNOWLEDGE BASE                   │
│         (Content with sensitivity markers)          │
└─────────────────────┬───────────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────────┐
│                   ACCESS CONTROL                    │
│        (RBAC: Role → Content scoping)               │
└─────────────────────┬───────────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────────┐
│                 OUTPUT GOVERNANCE                   │
│     (Scan agent outputs before disclosure)          │
└─────────────────────────────────────────────────────┘
```

---

## Use Cases

- **Enterprise AI Assistants** — Ensure agents don't leak confidential data
- **Multi-tenant Systems** — Scope agent knowledge by customer/role
- **Compliance Environments** — Audit what agents accessed and disclosed
- **Development Teams** — Separate internal process docs from public-facing content

---

## Framework Alignment

AgentKB governance primitives align with:
- NIST AI RMF (Risk Management Framework)
- OWASP LLM Top 10 (Sensitive Information Disclosure)
- Emerging AI governance standards

---

## Status

AgentKB is under active development. Current focus:
- ✅ Output governance scanning (operational)
- 🔄 RBAC content scoping (in progress)
- 📋 Closed-loop error→rule automation (planned)

---

## Getting Started

See the [documentation](docs/) for integration guides and API reference.

```bash
pip install agentkb
agentkb init
```

---

## License

MIT License. See [LICENSE](LICENSE) for details.

---

## Contributing

Contributions welcome. Please review contribution guidelines before submitting PRs.

---

*Built for a world where AI agents are principals, not just tools.*
