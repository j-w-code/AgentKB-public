# AgentKB — Provenance Attestation

**Owner:** Jeffrey Wiley Jr.
**Contact:** agentkb_jw@proton.me
**Date of Attestation:** March 5, 2026
**Attestation Generated (UTC):** 2026-03-05T22:13:46Z

---

## Project Identity

AgentKB is a governance-first knowledge infrastructure for AI agents,
independently conceived and developed by Jeffrey Wiley Jr.

- **Public Repository:** https://github.com/j-w-code/AgentKB-public
- **Private Repository:** https://github.com/j-w-code/AgentKB (source, tests, governance artifacts)
- **License:** Source-Available (see LICENSE and NOTICE)

---

## Development Timeline

- **Conceptual inception (JWKB design philosophy):** October 17, 2025
- **First commit (private repo):** December 31, 2025
- **First commit hash:** `b03856331dd6c1fb5520956a9d8b3ff984f25ef9`
- **First commit message:** `init: AgentKB governance-first knowledge infrastructure`

---

## Repository State at Attestation

- **Branch:** master
- **HEAD commit:** `3c6d584777be2d039842765837058354dd0509c3`
- **HEAD date:** 2026-03-05 14:16:39 -0600
- **HEAD message:** `Add Phase 6 productization track and roadmap coherence notes`
- **Total commits (all branches):** 416
- **Development span:** 2025-12-31 → 2026-03-05 (65 days of continuous development)
- **All commits authored by:** Jeffrey Wiley / J.W.

---

## Codebase Scale (at attestation)

- **Source modules:** 120 Python files, 30,818 lines
- **Test suite:** 79 test files, 24,248 lines
- **Governance configuration:** 17 YAML files
- **Documentation:** 10 Markdown files
- **Utility scripts:** 20 Python files
- **Total project files (excluding caches):** 300+

---

## Architectural Scope (public summary)

AgentKB implements governance enforcement for agentic AI workflows,
including but not limited to:

- Multi-boundary runtime governance (access, input, output, tool invocation)
- Role-based access control (RBAC) for AI agent principals
- Deterministic policy gates with audit-grade decision traceability
- Governance Compliance Score (GCS) computation
- Closed-loop error-to-rule governance pipeline
- Multi-provider LLM abstraction layer
- REST API, MCP server, and CLI interfaces
- 3-tier detection architecture (rule-based, semantic embeddings, LLM evaluation)
- Adversarial test corpus and property-based testing
- Kubernetes deployment configuration
- CI/CD with mutation testing, static analysis, and coverage enforcement

---

## Commit History Milestones (selected)

| Date | Hash (short) | Milestone |
|------|-------------|-----------|
| 2025-12-31 | `b0385633` | Initial commit: governance-first knowledge infrastructure |
| 2026-01-02 | `bd16a33f` | Scaffold agentkb Python package |
| 2026-01-02 | `6138dabf` | Init gate and output gate enforcement |
| 2026-01-04 | `ef1ae1a1` | Discovery scan MVP + RBAC MVP |
| 2026-01-06 | `1aa64fe5` | RBAC Phase 1 and 2 |
| 2026-01-15 | `805e9159` | GCS calculation module |
| 2026-01-18 | `ac0f4e14` | 3-tier detection architecture |
| 2026-01-18 | `b958046` | Semantic layer training complete |
| 2026-01-24 | `ae0a1e71` | Audit Bus primitive |
| 2026-01-25 | `b123cdb8` | v0.5.0 release (Phase 2.7 complete) |
| 2026-01-26 | `ba8cf635` | Phase 2.9 foundation hardening (8/8 items) |
| 2026-01-27 | `d3abb59f` | Four operational modes (true gate independence) |
| 2026-02-04 | `7685c4af` | Session-ID audit event origins (Phase 2.10.1) |
| 2026-02-20 | `5b8cf730` | Gate-internal decomposition refactor |
| 2026-02-24 | `9f9ae7ee` | Phase 3.11 governance indicator API |
| 2026-02-27 | `1c347aba` | ISLAND operational mode |
| 2026-03-02 | `3789748c` | Phase 4 hardening (lock enforcement + audit contracts) |
| 2026-03-05 | `3c6d5847` | Phase 6 productization track |

---

## Evidence Artifact Hashes

Full provenance evidence is maintained in the private repository.
SHA-256 hashes of the evidence artifacts generated at attestation time
are recorded here for integrity verification:

| Artifact | SHA-256 |
|----------|---------|
| Combined provenance snapshot | `47B2413597E1B03DC9DB5AA89D215A5CA60AF21E3921DBE010DAAAE707A206DA` |
| Full recursive file tree | `CB4601591EE2099C38AB802EF9B91C66587C38C4F898FCBF36307FEF9AFFBF1B` |
| Complete git commit log (416 entries) | `4DDEE30AC4BB0D67E9C8FB2ED07D29C25AE591D81C2FCFC657625F6A1E151520` |
| Repository statistics | `3C7B1D51B5D3FB4AB8E61FA4E2EB2DD491B1D70813C189F65F179B2A931DA2FA` |

These hashes can be verified against the private repository artifacts
upon request or in any legal proceeding.

---

## Prior Art and Independent Derivation

The core architectural patterns in AgentKB were independently derived
beginning October 2025, prior to any employment relationship with
Scale AI or any related entity:

- **Data-layer RBAC enforcement** for AI agents (claim type, sensitivity, path scoping)
- **Codex + Role governance layering** (universal floor with additive role restrictions)
- **RBAC as resource allocation proxy** (privilege tier = context budget + persistence + audit depth)
- **GOVERNANCE ≠ SAFETY** architectural separation
- **Closed-loop governance** (error → pattern → rule → codex)

Post-derivation literature review confirmed no identical prior implementation
(AIOS, Sendbird AI RBAC, Auth0 each address related but distinct problems).

---

## Purpose of This Document

This attestation establishes a timestamped public record of:

1. **Authorship** — Jeffrey Wiley Jr. as sole author of AgentKB
2. **Timeline** — Continuous development from December 31, 2025, with conceptual work from October 17, 2025
3. **Scope** — The scale and architectural breadth of the work product
4. **Integrity** — SHA-256 hashes linking to complete private evidence artifacts
5. **Independence** — Prior art status relative to any subsequent employment

This document is published to the public repository as a matter of record.
Full evidence (source code, commit history, file trees) is available in the
private repository and can be produced upon request.

---

*Jeffrey Wiley Jr. — March 5, 2026*
