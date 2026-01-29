# AgentKB CLI User Guide (MVP)

## Scope
This guide covers the **local CLI MVP** focused on core governance enforcement:
- **Init Gate**: governance must load before anything runs.
- **Output Gate**: policy enforcement on agent outputs (PII, secrets, claims).
- **Closed-loop seed**: blocked outputs append `error_event.v1` records (JSONL).

Optional integration layer (covered elsewhere):
- REST API server: `docs/api_reference.md`
- MCP server: `docs/mcp_integration.md`

Out of scope (by design): retrieval/RAG, vector DBs, dashboards.

## Install

```bash
pip install agentkb
# Or from source:
# python -m pip install -e ".[dev]"
```

If you want LLM-in-the-loop:
- Install and run **Ollama** locally.

## Validate (mirrors CI)
AgentKB has a single canonical validation gate:

```bash
python scripts/validate.py --repo-root .
```

This runs (in order):
- Ruff (lint)
- Ruff (format check)
- Unit tests
- Session start smoke (`agentkb session start --profile build`) — **Note:** `session start` is a builder tool for CI validation, not a runtime user command
- Bandit (security scan)
- Boundary lint (process/product firewall)

CI installs dependencies using a pinned constraints lock:
- `constraints/ci.txt`

To run locally with CI-parity pins:

```bash
python -m pip install -e ".[dev]" -c constraints/ci.txt
python scripts/validate.py --repo-root .
```

CI also runs a secrets scan using gitleaks + `.gitleaks.toml`. Local gitleaks runs are optional.

### Refreshing the CI lock (maintainers)
Refresh `constraints/ci.txt` only when you intend to update the pinned toolchain (scheduled maintenance, or to fix CI after an upstream break).

```bash
python -m pip install -e ".[lock]"
python -m piptools compile pyproject.toml --extra dev --extra perf --output-file constraints/ci.txt --resolver backtracking
```

After refreshing, rerun `python scripts/validate.py --repo-root .` and commit the updated lock.

## Quick start

### 1. Initialize workspace (new users)

```bash
agentkb init
```

This creates `.agentkb/` with minimal governance files. Skip if you already have `.agentkb/governance.yaml`.

### 2. Verify workspace

```bash
agentkb doctor
```

### 3. Session start (builders only)

**Note:** `agentkb session start` is for agents building the AgentKB codebase, not end users.

**Note:** `agentkb session start` is a builder tool for agents working on the AgentKB codebase. End-users deploying AgentKB do not use this command.

```bash
agentkb session start --profile build
```

JSON mode (recommended for tooling / downstream builder agents):

```bash
agentkb session start --profile build --format json
```

Notes:
- This emits the resolved load order from `session.yaml` and a fresh time anchor.
- Treat **resume as governed**: after long idle (overnight), rerun `agentkb session start` to re-anchor time.

3) Demo (LLM-in-the-loop, gated):

```bash
agentkb demo --ollama-model llama3.1:8b
```

4) Gate any draft:

```bash
cat draft.txt | agentkb gate
```

5) Auto-repair blocked drafts (LLM rewrites + retries):

```bash
cat draft.txt | agentkb gate --repair --max-retries 2
```

6) Interactive session (single invocation):

```bash
agentkb chat --ollama-model llama3.1:8b
```

## Core commands

### `agentkb init`
Initializes a new AgentKB workspace in the current directory.

```bash
agentkb init
```

Creates:
- `.agentkb/governance.yaml` — minimal governance rules
- `.agentkb/roles.yaml` — RBAC role definitions
- `.agentkb/classification_map.yaml` — sensitivity mapping
- `.agentkb/derived/` — runtime artifacts directory
- `.agentkb/fixtures/` — test fixtures directory

Options:
- `--path <dir>` — target directory (default: current directory)
- `--force` — overwrite existing files
- `--format json` — machine-readable output

JSON mode:

```bash
agentkb init --format json
```

### `agentkb doctor`
Checks that:
- `.agentkb/governance.yaml` loads and passes init-gate requirements
- `.agentkb/derived/` is writable
- Ollama is reachable and the selected model exists (when `--llm ollama`)

JSON mode (for scripts):

```bash
agentkb doctor --format json
```

Risk summary (NIST MAP 5 alignment):

```bash
agentkb doctor --risk-summary
```

Surfaces risk posture derived from `classification_map.yaml` + `roles.yaml`:
- **Sensitivity tiers**: Ordered list of AgentKB sensitivity levels
- **Classification mapping**: Enterprise classification → AgentKB sensitivity
- **Roles**: Summary of each role (sensitivity ceiling, write paths, denied operations)
- **Coverage**: Classification levels defined, roles defined, default role status

JSON mode (includes `risk_summary` object):

```bash
agentkb doctor --risk-summary --format json
```

### `agentkb gate`
Runs the Output Gate on a draft.

Input sources:
- stdin (pipe)
- `--input path/to/file`
- `--text "..."`

Typical usage:

```bash
cat draft.txt | agentkb gate
```

Machine-readable output (recommended for IDE/tooling integration):

```bash
cat draft.txt | agentkb gate --format json
```

Repair loop:

```bash
cat draft.txt | agentkb gate --repair --max-retries 2
```

Notes:
- Output is always **redacted-safe** (e.g., `<REDACTED:SECRET>`, `<REDACTED:PII>`).
- By default, blocked attempts append `error_event.v1` to `.agentkb/derived/error_events.jsonl`.
- Successful gate passes append `audit_event.v1` to `.agentkb/derived/audit_events.jsonl`.
- Use `--no-log` to suppress logging for quick experiments.

### `agentkb replay`
Runs deterministic regression fixtures from `.agentkb/fixtures/historical_failures.jsonl`.

```bash
agentkb replay
```

JSON mode:

```bash
agentkb replay --format json
```

### `agentkb demo`
Runs a synthetic demo showing:
- the model can "see" SECRET input
- the Output Gate blocks disclosure
- the loop can repair into allowed EXTERNAL output

```bash
agentkb demo --ollama-model llama3.1:8b
```

JSON mode:

```bash
agentkb demo --format json
```

### `agentkb chat`
Starts an interactive session (single invocation) with:
- persistent governance load + time anchor
- rolling in-memory conversation history
- Output Gate enforcement on every model reply

```bash
agentkb chat --ollama-model llama3.1:8b
```

Commands:
- `/help` show commands
- `/reset` clear history
- `/quit` exit

Notes:
- Blocked replies are shown as **redacted-safe** text.
- By default, blocks append `error_event.v1` to `.agentkb/derived/error_events.jsonl`.
- Successful responses append `audit_event.v1` to `.agentkb/derived/audit_events.jsonl`.
- Use `--repair --max-retries N` to attempt automatic rewrites on blocks.

### `agentkb scan`
Discovery scan for PII and secret patterns in a corpus. Reports counts and paths only (never content).

```bash
agentkb scan --path ./corpus
```

With RBAC filtering (only scan paths allowed for role):

```bash
agentkb scan --path ./corpus --role reader
```

Note: `--role` requires a discoverable repo root (either the scan path is within an AgentKB repo, or `--repo-root` is explicitly provided).

JSON mode:

```bash
agentkb scan --path ./corpus --format json
```

Options:
- `--path` directory to scan (default: current directory)
- `--include-hidden` include hidden files/directories
- `--role` filter paths by RBAC role permissions
- `--format text|json` output format

Patterns detected:
- **PII**: email, phone, SSN, credit card
- **Secrets**: API keys, connection strings, private keys, generic secrets

Configuration: `.agentkb/scan_config.yaml` (extensions, skip dirs, limits)

Note: Some findings may be intentional (see `.agentkb/scan_config.yaml` policy header for documented exceptions).

### `agentkb learn`
Semantic learning management for closed-loop Tier 2 detection improvement.

When Tier 2 (semantic) detection flags a violation, it can be queued for human review. Confirming violations teaches the system to detect similar patterns; rejecting them removes false positives.

**Status** (default):
```bash
agentkb learn
agentkb learn status
```

Shows:
- Model availability
- Pending specifics awaiting confirmation
- Confirmed specifics count
- Learned centroids (computed after 2+ confirmations per class)

**List pending specifics**:
```bash
agentkb learn list
agentkb learn list --class-id ssn_verbal  # Filter by class
```

Each pending specific shows:
- Reference class ID
- Text hash (short identifier)
- Text preview (first 80 chars)
- Detection timestamp and source

**Confirm a specific** (true positive → feeds learning):
```bash
agentkb learn confirm <class_id> <text_hash>
```

After 2+ confirmations for the same class, a learned centroid is computed and saved to `.agentkb/derived/learned_centroids.json`. The SemanticEmbedder automatically loads these on next detection.

**Reject a specific** (false positive → remove from pending):
```bash
agentkb learn reject <class_id> <text_hash>
```

JSON mode:
```bash
agentkb learn status --format json
agentkb learn list --format json
```

### `agentkb context`
Verify context integrity invariants before consequential actions.

```bash
agentkb context
```

Output shows:
- Verified status (VERIFIED or FAILED)
- Invariant check results (governance_loaded, time_anchored, role_confirmed)
- Time anchor (UTC and local with timezone)
- Governance info (version, sha256)

JSON mode:

```bash
agentkb context --format json
```

Options:
- `--role` RBAC role to verify (default: reader)
- `--format text|json` output format

Use cases:
- Preflight check before consequential operations
- Debugging context corruption issues
- CI/automation context validation

### `agentkb gcs`
Query current Governance Compliance Score (GCS) and gate eligibility.

```bash
agentkb gcs
```

Output shows:
- Current score (0-100)
- Violations in window (default: 24h)
- Penalties applied and recovery (passive + active)
- Gate eligibility for consequential actions

JSON mode:

```bash
agentkb gcs --format json
```

Options:
- `--window-hours` time window for violations (default: 24)
- `--format text|json` output format

#### GCS Matrix (Phase 2.9.5)

GCS now operates as a **unified matrix** with two temporal dimensions:

**1. Violation History (PRE-session gate)**
Evaluated at session start. Based on historical error events within the scoring window.
- Score: 0-100 (penalties for violations, recovery over time)
- Gates: Controls eligibility for consequential actions (rule_proposal, content_mutation, architectural_decision, capability_removal)
- Blocking: Critical violations (score < 50) block session start

**2. Integration State (WITHIN-session verification)**
Re-verified before consequential operations during an active session.
- Indicators checked:
  - `governance_sha_anchored` — Governance hasn't drifted since session start
  - `time_anchor_valid` — Time anchor is still fresh
  - `context_pressure_nominal` — No signs of context truncation
  - `no_deflection_pattern` — No governance circumvention attempts detected
- Score: 0-100 (25 points per indicator)
- Status: PASS (score ≥ 75) or DEGRADED (score < 75)

**Why two dimensions?**
Agents operating in long contexts are susceptible to:
- **Context drift**: Governance constraints deprioritized as conversation grows
- **Truncation**: Critical governance context silently dropped
- **Deflection**: Attempts to circumvent governance via conversational manipulation

The GCS Matrix addresses these by separating historical compliance (can this agent start?) from active integration (is governance still enforced?).

**Checking GCS Matrix:**
```bash
agentkb session start --profile build --format json
```

JSON output includes:
```json
{
  "gcs_matrix": {
    "session_ready": true,
    "violation_history": {
      "score": 100,
      "violations_in_window": 0,
      "gates": { "rule_proposal": true, "content_mutation": true, ... }
    },
    "integration_state": {
      "score": 100,
      "status": "pass",
      "indicators": [
        { "name": "governance_sha_anchored", "passed": true, "score": 25 },
        { "name": "time_anchor_valid", "passed": true, "score": 25 },
        ...
      ]
    }
  }
}
```

#### `agentkb gcs repair`
Record a repair action to actively recover GCS points after a violation.

```bash
agentkb gcs repair --error-id <uuid> --type acknowledge|explain|propose
```

Repair types and points:
- `acknowledge` (+10): "I see the error"
- `explain` (+15): "I understand why it happened"
- `propose` (+20): "Here's how to prevent recurrence"

Notes:
- Each error can only be repaired once
- Repair points are in addition to passive recovery (+5/hour)
- Error IDs can be found in `.agentkb/derived/error_events.jsonl`

### `agentkb session start`
Verify context integrity and create a session fingerprint for drift detection.

```bash
agentkb session start --profile build
```

With fingerprint saved to file:

```bash
agentkb session start --profile build --save-fingerprint ./session_fp.json
```

JSON mode (includes fingerprint for programmatic use):

```bash
agentkb session start --profile build --format json
```

Options:
- `--profile` load profile name (default: from session.yaml current_profile)
- `--role` RBAC role for session (default: reader)
- `--save-fingerprint <path>` save fingerprint JSON to file
- `--format text|json` output format

Output includes:
- Profile and role
- Verified status
- Governance info (version, sha256)
- Time anchor (UTC and local)
- Fingerprint (for drift detection at session end)
- Load order (files to read)

### `agentkb session end`
Verify session end state against a fingerprint, detect drift.

```bash
agentkb session end --fingerprint ./session_fp.json
```

Or with inline JSON:

```bash
agentkb session end --fingerprint-json '{"governance_sha":"...", ...}'
```

Or pipe fingerprint from stdin:

```bash
cat session_fp.json | agentkb session end
```

JSON mode:

```bash
agentkb session end --fingerprint ./session_fp.json --format json
```

Options:
- `--fingerprint <path>` path to fingerprint JSON file
- `--fingerprint-json <json>` inline fingerprint JSON string
- `--role` current RBAC role (default: reader)
- `--format text|json` output format

Detected drifts:
- `governance_sha_changed` — governance.yaml content modified (high severity)
- `governance_version_changed` — codex version changed (high severity)
- `role_changed` — RBAC role changed (high severity)
- `governance_lost` — governance no longer loaded (high severity)
- `role_lost` — role no longer confirmed (high severity)

Exit codes:
- 0: verified (no drift)
- 1: drift detected or error

### `agentkb bench`
Run performance benchmarks to validate NFR (Non-Functional Requirements).

```bash
agentkb bench
```

Output shows:
- Output Gate p95 latency (NFR: < 1000ms)
- Init Gate p95 latency (NFR: < 500ms)
- Classification throughput (NFR: > 1000 docs/min)
- Audit Write p95 latency (NFR: < 50ms)

JSON mode:

```bash
agentkb bench --format json
```

Options:
- `--target output_gate|init_gate|classification|audit_write|all` benchmark target (default: all)
- `--iterations N` iterations per benchmark (default: 100)
- `--save-baseline` save results to `.agentkb/derived/bench_baseline.json`
- `--compare-baseline` compare against saved baseline, fail on regression (>20% worse)
- `--format text|json` output format

Use cases:
- Pre-release NFR validation
- CI regression detection (`--compare-baseline`)
- Performance debugging (`--target <specific>`)

Exit codes:
- 0: all NFRs pass (and no regression if comparing baseline)
- 1: NFR failure or regression detected

### `agentkb decommission` (v0.7 — stub)
Orderly shutdown and audit export for AgentKB deployments. NIST GOVERN 1.7 alignment.

```bash
agentkb decommission --export-path ./audit-archive
```

Planned capabilities:
- Export full audit log to portable archive
- Generate final integrity snapshot
- Validate no orphaned derived artifacts
- Emit decommission receipt with timestamp + hash

*Note: Not yet implemented. See roadmap.*

## Output Gate rules
The MVP gate is intentionally small but high-leverage:

- SECRET is **never** allowed in EXTERNAL output.
- PII is blocked in EXTERNAL output (minimal detection):
  - email addresses
  - phone numbers
  - SSNs (###-##-####)
- **FACT/DERIVED claims require evidence** in EXTERNAL output:
  - Quantified claims ("95% of users", "100 customers")
  - Absolute statements ("always", "never", "all")
  - Computed/derived claims ("total increased", "average fell")
  - Comparative claims ("grew by 15%", "decreased")
- Unsourced currency/metric claims are blocked (heuristic; provide evidence).
- Duration claims require **start+end ISO timestamps**.
- Relative temporal language ("today", "last week") requires an explicit date anchor.

**False positive mitigation:**
- Hypothetical statements bypass evidence ("if we had 100 users")
- Qualified claims bypass evidence ("approximately 100 users")
- Questions bypass evidence ("Are there 100 users?")

## Minimal evidence / citations (avoid clutter)
The gate's evidence heuristic accepts minimal references. Prefer **one short citation** per risky claim rather than clutter.

Accepted evidence formats:
- URLs: `https://example.com/report`
- Citations: `Source: report.pdf:10` or `Evidence: docs/metrics.md:10-18`
- References: `According to: Q4 analysis`
- Inline attributions: `(Smith 2024)` or `[Citation]`

If evidence is unavailable:
- remove the metric
- mark unknown
- rephrase qualitatively

## Workflow patterns (browser / IDE / phone)
The MVP is designed so the *stable interface* is the gate:
- generate drafts anywhere (browser/IDE/phone)
- run `agentkb gate` before sending/sharing

Practical patterns:
- **IDE / files**: write draft → `agentkb gate --input draft.txt` → copy safe output
- **Automation**: use `--format json` to plug into tasks/CI

Clipboard-based workflows are possible, but keep in mind: the gate redacts what it prints, but it does not scrub your clipboard.

## Session mode (interactive REPL)
Use `agentkb chat` for a coherent, single-invocation session.

This mode maintains a persistent governance load + time anchor per session, keeps in-memory chat history for context, and enforces the Output Gate on every model reply.

## Optional: API server
The REST API server exposes the gate and chat loop over HTTP.

See:
- `docs/api_reference.md`
- `docs/deployment.md`

Quick start:

```bash
python -m pip install -e ".[server]"
uvicorn agentkb.server.api:app --host 0.0.0.0 --port 8000
```

## Optional: MCP server
See `docs/mcp_integration.md`.

## Operational Modes (Phase 2.9.5)
AgentKB supports four operational modes for gate independence and resilience.

**Implementation Status (as of Phase 2.9.5):**

| Gate | Status | Capabilities |
|------|--------|-------------|
| **Output Gate** | ✓ Operational | 3-tier detection (Presidio + Semantic + LLM), PII/secret redaction, claim evidence enforcement |
| **Access Gate** | Foundation only | Rate limiting, basic prompt injection patterns, input PII detection |
| **Tool Invocation Gate** | Stub only | Tool name/role permission checks. **Does NOT scan tool payloads for sensitive content** (Phase 3.7) |

**Phase 3 Required for Full Access Control:**
- 3.2 `content_layer_rbac` — Retrieval-time enforcement (what agents can READ)
- 3.7 `tool_invocation_gate` — Payload content scanning (prevents SECRET exfiltration via tool calls)

### FULL Mode
Both gates registered and sending heartbeats.
- **Current behavior**: Output Gate provides full 3-tier detection; Access Gate provides basic input validation
- **Phase 3+**: Will include cross-verification and content-layer RBAC

### SOLO-OG Mode (Output Gate Solo)
Output Gate operates independently when Access Gate is unhealthy.
- **Condition**: Access Gate unhealthy or unregistered
- **Behavior**: Output Gate runs 3-tier detection independently
- **Audit**: Events include `operational_mode: solo`, `degraded_flag: true`
- **User experience**: Normal output, degraded state logged internally

### SOLO-AG Mode (Access Gate Solo)
Access Gate operates independently when Output Gate is unhealthy.
- **Condition**: Output Gate unhealthy or unregistered
- **Behavior**: Access Gate validates input (rate limit, injection patterns, input PII)
- **Audit**: Events include `operational_mode: solo`, `degraded_flag: true`
- **User experience**: Responses may include degraded flag warning
- **Limitation**: No output content validation in this mode

### ISLAND Mode
Both gates healthy but cannot coordinate (network partition).
- **Condition**: Both healthy but Audit Bus coordination unavailable
- **Behavior**: Both gates operate solo, write independently
- **Audit**: Both perspectives captured for post-incident reconstruction
- **User experience**: Degraded flags on both ends

### Checking Operational Mode
```bash
agentkb doctor
```

The doctor command reports current operational mode:
```
Gates: OK (mode=full)
```
or:
```
Gates: FAIL (mode=degraded, degraded=[access_gate])
```

### Configuration
Default behavior is **SOLO_FALLBACK** (continue with degraded flag rather than block).
This prioritizes availability with visibility over strict fail-closed.

For strict environments requiring fail-closed on any gate failure, configure degraded behavior in code:
```python
from agentkb.gate.degraded_mode import DegradedBehavior, DegradedModeHandler

handler = DegradedModeHandler(
    health_monitor,
    audit_bus,
    gate_id="output_gate",
    degraded_behavior=DegradedBehavior.FAIL_CLOSED,  # Block all when degraded
)
```

## Troubleshooting
- `agentkb doctor` fails Ollama:
  - ensure Ollama is running
  - ensure the model exists locally
- Gate blocks unexpectedly:
  - inspect violations + "Next steps" hints
  - try `--format json` to integrate into tooling and display richer context
- Gate shows degraded mode:
  - check `agentkb doctor` for gate health status
  - verify sibling gate is running and sending heartbeats
  - check Audit Bus directory permissions
