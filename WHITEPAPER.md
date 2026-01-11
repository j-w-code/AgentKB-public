# AgentKB: Governance-First Knowledge Infrastructure for AI Agents

> **Status:** DRAFT — Under development

**Jeffrey Wiley**  
Dallas-Fort Worth, Texas  
jeffreyjaywork@gmail.com

---

## Abstract

*[~200 words: Problem statement, solution approach, key results]*

---

**Keywords:** AI governance, agentic architecture, knowledge infrastructure, RBAC, closed-loop governance

---

## I. Introduction

*[Agentic AI adoption bottleneck, current failure modes, Access ≠ Disclosure thesis]*

---

## II. Related Work

### A. Vector Databases

*[Pinecone, Weaviate — retrieval-optimized, not governance-aware]*

### B. RAG Frameworks

*[LangChain, LlamaIndex — pipelines without policy enforcement]*

### C. The Governance Gap

*[Why this isn't their problem to solve; why a new primitive is needed]*

---

## III. Architecture

### A. Core Primitives

*[Claim typing, sensitivity tiers, manifest-first traversal]*

### B. Layered Enforcement

*[Codex → Role → Request evaluation order]*

### C. Closed-Loop Governance

*[Error → Pattern → Rule → Codex → Enforcement]*

---

## IV. Implementation

### A. Output Gate

*[Access ≠ Disclosure enforcement at output time]*

### B. Role-Based Access Control

*[Sensitivity ceilings, path scoping, fail-closed defaults]*

### C. Discovery Scan

*[PII/secret pattern detection, counts-only reporting]*

### D. Error Event Logging

*[Structured JSONL, replay harness, historical regression]*

---

## V. Evaluation

### A. Framework Alignment

*[NIST AI RMF 1.0 — GOVERN, MAP, MEASURE]*

*[OWASP Agentic Security Initiative 2026 — 8/10 coverage]*

### B. External Validation

*[Enterprise feedback signals, independent derivation confirmation]*

### C. Demo Methodology

*[Fresh VM deployment, real LLM integration — see DEMO_METHODOLOGY.md]*

---

## VI. Discussion

### A. Current Limitations

*[Point-in-time classification, single-process sessions, no multi-tenant yet]*

### B. Future Work

*[Phase 2: Multi-tenant, provider abstraction, audit query interface]*

*[Phase 3: Tamper-proof audit, IdP integration, inter-agent security]*

---

## VII. Conclusion

*[Summary of contribution, governance as infrastructure not afterthought]*

---

## Acknowledgments

*[If applicable]*

---

## References

*[To be populated]*

---

*© 2025-2026 Jeffrey Wiley. All rights reserved.*
