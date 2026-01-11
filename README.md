# AgentKB

> ⚠️ **PUBLIC REPOSITORY** — This repo contains high-level overview only. Detailed specifications are proprietary.

**Governance-first knowledge infrastructure for AI agents.**

🎬 **[Watch the Demo](https://youtu.be/69m-_s-wf8A)** (1:24) — See the Output Gate block secrets and PII in real-time.

---

## The Problem

AI agents fail predictably when:

1. **Data is unstructured** → Agents hallucinate facts
2. **Policies are implicit** → Agents leak PII and secrets  
3. **Verification is absent** → Outputs can't be traced to sources
4. **Constraints aren't enforced** → Scope drift corrupts accuracy

Current solutions don't address this:
- **Vector databases** (Pinecone, Weaviate) optimize for retrieval, not governance
- **RAG frameworks** (LangChain, LlamaIndex) are pipelines without policy enforcement
- **Knowledge tools** (Notion, Obsidian) are human-first, agent-hostile

**The gap:** No existing tool treats governance as a first-class feature for AI agent knowledge access.

---

## The Solution

AgentKB is infrastructure that makes AI agents safe by default—through architecture, not prompting.

### Core Primitives

| Primitive | What It Does |
|-----------|--------------|
| **Manifest-first traversal** | Explicit navigation graph prevents arbitrary exploration |
| **Claim typing** | FACT/DERIVED/INFERENCE boundaries at the data layer |
| **Policy gates** | Sensitivity rules enforced at retrieval time |
| **Integrity verification** | Hashes, UUIDs, audit trails built-in |
| **Closed-loop governance** | Errors become rules; the system learns from failures |

### Key Differentiator

Traditional approach: *"Prompt your agent not to leak data"*  
AgentKB approach: *"Agent cannot access data it shouldn't see"*

The difference is enforcement vs. suggestion.

---

## Architecture Overview

```
User Data → Schema Layer → Governance Layer → Agent Interface
                              ↑                    │
                              │                    ↓
                        Rule Generator ←── Error Log
```

**Schema Layer:** What exists (manifests, document typing, relationships)  
**Governance Layer:** What's allowed (policies, constraints, audit rules)  
**Agent Interface:** How agents access (traversal, retrieval, verification)  
**Feedback Loop:** How the system improves (error → pattern → rule)

See [ARCHITECTURE.md](ARCHITECTURE.md) for conceptual details.

---

## Status

**Stage:** Pre-seed / MVP  
**Current:** Working CLI with Output Gate, RBAC, discovery scan, and closed-loop error logging  
**Validation:** Architecture aligns with NIST AI RMF 1.0 and OWASP Agentic Security Initiative 2026  
**Next:** API layer, SDK, enterprise pilot

---

## Why Now?

2024-2025 saw massive growth in agentic AI deployment. But infrastructure hasn't caught up:
- Enterprises can't deploy RAG safely (compliance concerns)
- No standard for agent-friendly knowledge structure
- "Prompt engineering for safety" doesn't scale

The governance gap is the bottleneck for enterprise AI adoption.

---

## Contact

**Jeffrey Wiley**  
📧 jeffreyjaywork@gmail.com  
📍 Dallas-Fort Worth, Texas

---

*© 2025-2026 Jeffrey Wiley. All rights reserved.*
