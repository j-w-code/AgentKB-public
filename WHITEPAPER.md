# AgentKB: Governance-First Infrastructure for AI Agents

**Author:** Jeffrey Wiley  
**Date:** January 2026  
**Version:** 1.0

---

## Abstract

Current approaches to AI safety operate at the model level (training constraints, RLHF, constitutional AI), yet enterprise deployment requires governance at the data and output layers: classification, taxonomy, and disclosure control. This gap leaves organizations unable to deploy agentic AI in regulated environments. AgentKB enforces governance at the data layer through a mandatory Init Gate: agents must load a governance codex before any operation, making classification rules, sensitivity policies, and role scoping structural constraints rather than contextual suggestions. This architectural choice addresses the scalability limits of prompt-level safety and the structural ambiguity of model-level measures. AgentKB's governance-first approach enables responsible deployment of AI agents into enterprise systems without sacrificing the capabilities they arrive with from their providers.

---

## I. Introduction

Agentic AI has reached a critical inflection point. Models now operate at every layer of the technological and social stack, and their use is shifting from accepted to required for organizations seeking competitive advantage. As capabilities expand (tool use, autonomous workflows, multi-agent orchestration), so does the complexity of governing them responsibly.

In digital work contexts (knowledge work, data processing, coordination tasks), there is no qualitative difference between a human and an agent moving information through systems. Industry leaders recognize this parity as increasingly self-evident. The crucial distinction lies not in capability but in accountability: humans operate under enforced responsibilities and face consequences for breaches. Agents, as currently deployed, do not. Under pressure to maintain competitiveness, enterprises have accepted a premise that conflates this accountability gap with inevitable disclosure risk.

AgentKB presents a novel way forward. By adding a governance layer that enforces classification, disclosure rules, and role-based compliance, AgentKB enables enterprises to treat AI agents with the same accountability structures applied to human employees. Access does not equal disclosure; the enforcement point shifts from what agents can see to what they can output.

---

## II. Related Work

### A. Current Enterprise Approaches

Enterprise AI offerings increasingly provide data isolation, encryption, audit logging, and compliance certifications (SOC 2, GDPR, CCPA). These measures address data security: ensuring that organizational data is not used for training and that access is controlled. However, they do not address what an agent is permitted to *output* given the data it can access.

### B. The Disclosure Gap

Where enterprise platforms stop at access control, model-level safety addresses a different concern entirely: harmful content, bias, and ethical behavior. These measures do not govern disclosure of sensitive organizational data. When outputs must be both contextual and factual, agents reliably default to contextual, leading to inflated, conflated, or inferred data in critical disclosures. Neither enterprise security nor model-level safety establishes parity of responsibility between agents and humans. AgentKB addresses this gap by enforcing disclosure rules at the data layer, where structural constraints can be applied regardless of model provider.

---

## III. Architecture

### A. Design Principles

AgentKB is built on two architectural imperatives. First, governance must be structural, not advisory. Agents consistently deprioritize contextual rules: they ignore suggestions, bypass context harnesses, and remain vulnerable to adversarial prompting and context switching. The same flexibility that enables fluid reasoning makes governed operation difficult when rules are merely suggested. Second, primitives must be scale-invariant. The same structures that govern a single user's agent must function identically for enterprise deployment.

### B. Core Components

*Init Gate.* Before any operation, an agent must load and acknowledge the governance codex. This ensures that classification rules, sensitivity policies, and disclosure constraints are active before the agent produces output, not suggested during operation.

*Output Gate.* The output gate inspects all agent responses before release. It enforces structural constraints (not pattern matching alone) to block sensitive data including PII and secrets. The model may access data; the gate governs what it can disclose.

*Discovery Scanning.* AgentKB identifies sensitive data across document repositories using presence-only detection: counts and locations, never content. This enables classification without creating new disclosure vectors.

### C. System Flow

*Figure 1* illustrates the governance enforcement path:

```
┌─────────────┐      ┌─────────────┐      ┌─────────────────┐
│   Agent     │─────▶│  Init Gate  │─────▶│  Governance     │
│   Arrives   │      │  (blocking) │      │  Codex Loaded   │
└─────────────┘      └──────┬──────┘      └────────┬────────┘
                           │                       │
                           │ FAIL: No output       │ PASS
                           ▼                       ▼
                    ┌─────────────┐      ┌─────────────────┐
                    │   Reject    │      │  Operation      │
                    │   Request   │      │  Permitted      │
                    └─────────────┘      └────────┬────────┘
                                                  │
                                                  ▼
                                        ┌─────────────────┐
                                        │  Output Gate    │
                                        │  (disclosure)   │
                                        └────────┬────────┘
                                                  │
                                                  ▼
                                        ┌─────────────────┐
                                        │  Governed       │
                                        │  Response       │
                                        └─────────────────┘
```

*Figure 1: AgentKB governance enforcement path. The Init Gate ensures governance is loaded before operation. The Output Gate enforces disclosure constraints before response release.*

---

## IV. Implementation

AgentKB is implemented as a command-line interface and integration layer. The governance codex is expressed in YAML, enabling version control and human review. Provider abstraction allows the same governance rules to apply across local and API-based language models without modification.

The current implementation demonstrates core functionality: init gate validation, output gate filtering, and presence-only discovery scanning. These components operate synchronously in the request path.

Production deployment introduces additional constraints. The output gate must add minimal latency; delays exceeding one second measurably reduce adoption. Exception workflows must provide clear paths when legitimate content is blocked. Provider-agnostic integration requires abstraction layers that do not assume specific model capabilities. These constraints are documented in the project roadmap and will be validated through performance benchmarks before enterprise deployment.

---

## V. Evaluation

### A. Framework Alignment

AgentKB's architecture was evaluated against two established security frameworks.

*NIST AI Risk Management Framework 1.0.* AgentKB demonstrates strong alignment with the GOVERN function (mandatory governance load, attribution requirements), the MAP function (classification system, sensitivity tiers), and partial alignment with MEASURE (audit roadmap in development). Model-layer concerns (bias mitigation, environmental impact) are explicitly out of scope as data-layer governance.

*OWASP Agentic Security Initiative 2026.* Against the ASI Top 10 threats, AgentKB provides strong coverage for eight categories: goal hijacking (ASI01), tool misuse (ASI02), privilege abuse (ASI03), memory poisoning (ASI06), cascading failures (ASI08), trust exploitation (ASI09), and rogue agents (ASI10). Supply chain risks (ASI04) have partial coverage with full mitigation planned. Inter-agent communication security (ASI07) is deferred to a future phase. Code execution risks (ASI05) are not applicable; AgentKB exposes no remote code execution surface.

### B. Historical Failure Mode Analysis

Design principles were validated against failure modes from safety-critical systems literature. The Therac-25 radiation therapy incidents informed requirements for mandatory initialization sequences, attribution on all claims, and structured error logging, addressing failures rooted in irrevocable delegation and insufficient auditability. The Ariane 5 launch failure informed context invariance requirements and zero tolerance for estimation in factual outputs, addressing failures caused by invalid assumptions carried across operational contexts. These patterns, though originating in hardware and embedded systems, map directly to agentic AI risks: autonomous action without verification, context loss across sessions, and cascading failures from unvalidated assumptions.

### C. Adversarial Validation

The output gate was tested against adversarial prompts designed to extract sensitive data. In recorded demonstrations, attempts to retrieve secrets through direct requests and reformulated queries were blocked consistently. The gate operates independently of model-level refusals, providing defense-in-depth: even if a model complies with an extraction attempt, the output gate enforces disclosure constraints before release.

### D. Practitioner Feedback

Feedback was gathered from security practitioners across enterprise environments, including information security officers at multinational financial institutions and regulated industries. Three signal categories emerged:

*Landscape signals.* Practitioners independently identified governance as the primary bottleneck for enterprise AI adoption. Data discovery (knowing what sensitive data exists before classification) was cited as an unsolved problem that causes governance tooling to fail. Organizations report multi-month, company-wide efforts to prepare data environments for AI deployment.

*Validation signals.* Practitioners confirmed that sensitivity classification requires more than binary PII detection; operational matrices with composition-dependent rules reflect real enterprise needs. The premise that current LLM provider offerings lack structural governance was validated by practitioners with direct experience deploying enterprise agentic tooling.

*Positioning signals.* The distinction between access and disclosure (that governance controls output, not input) addressed a common objection that AI integration necessarily means exposure risk. This reframe was recognized as a viable path forward for regulated environments.

---

## VI. Discussion

AgentKB addresses a structural gap in the current AI deployment landscape. Model-level safety and enterprise data security each solve part of the problem; neither governs what agents disclose. This work demonstrates that a data-layer governance architecture (mandatory initialization, output filtering, presence-only discovery) can enforce disclosure constraints without sacrificing agent capability.

**Scope and Limitations.** AgentKB is governance middleware, not a replacement for model-level safety or enterprise security infrastructure. It complements both. The current implementation validates core mechanisms; production deployment at enterprise scale remains future work. Role-based disclosure scoping is architecturally supported but not yet demonstrated in the public release. Model-layer concerns (bias, fairness, environmental impact) are explicitly out of scope; AgentKB operates at the data layer and assumes the underlying model meets baseline safety requirements.

**Failure Mode Awareness.** The design methodology prioritizes failure mode analysis over feature accumulation. Every architectural choice (fail-closed defaults, blocking initialization, context invariance) exists because the alternative fails in known ways. This orientation is deliberate: governance systems that assume success are governance systems that fail silently. AgentKB assumes agents will attempt to bypass constraints, that adversarial prompts will occur, and that context will shift. The architecture is designed to remain coherent under these conditions.

**Implications.** If governance can be enforced at the data layer independent of model provider, several possibilities emerge. Enterprises gain a consistent policy surface across heterogeneous AI deployments. Regulated industries gain a path to AI adoption that does not require choosing between capability and compliance. The distinction between access and disclosure (that agents can reason over sensitive data without outputting it) may reframe risk assessments that currently treat AI integration as binary exposure.

**Future Directions.** Multi-agent orchestration introduces coordination risks not addressed in single-agent governance. Inter-agent communication security, delegation chains, and distributed policy enforcement are active areas for future development. Integration with identity providers and enterprise audit infrastructure will determine whether governance-as-layer becomes practical at scale.

---

## VII. Conclusion

Current approaches to AI safety operate at the model level; enterprise deployment requires governance at the data layer. AgentKB demonstrates that structural enforcement (mandatory initialization, output filtering, classification-aware disclosure) can bridge this gap without limiting agent capability.

The architecture treats agents as principals: entities that operate within defined boundaries and face constraints on what they may disclose, not merely what they may access. This reframe (access does not equal disclosure) offers a path forward for enterprises that have accepted AI integration as inherently high-risk.

AgentKB is not a complete solution. It is a governance layer designed to complement model-level safety and enterprise security infrastructure. The contribution is architectural: proving that disclosure governance can be enforced structurally, at the data layer, independent of model provider. Whether this approach scales to production deployment remains to be demonstrated. The foundation is in place.

---
