# AgentKB User Guide

**For complete beginners.** This guide assumes no prior experience with Python packages, AI APIs, or AgentKB.

**Version:** 0.5.1 (Phase 2.9.5)

---

## Table of Contents

1. [What is AgentKB?](#what-is-agentkb)
2. [Prerequisites](#prerequisites)
3. [Installation](#installation)
4. [Quick Start (5 Minutes)](#quick-start-5-minutes)
5. [Setting Up Your LLM](#setting-up-your-llm)
6. [Core Workflows](#core-workflows)
7. [Command Reference](#command-reference)
8. [Understanding Detection](#understanding-detection)
9. [Operational Modes](#operational-modes)
10. [Monitoring & Compliance](#monitoring--compliance)
11. [Expected Behavior](#expected-behavior)
12. [Troubleshooting](#troubleshooting)
13. [Glossary](#glossary)

---

## What is AgentKB?

AgentKB is a **governance layer** for AI agents. Think of it as a compliance filter that reviews everything an AI reads and writes.

### What It Does

| Protection | How It Works |
|------------|--------------|
| **Blocks sensitive data** | Catches PII (emails, SSNs, phone numbers), passwords, API keys |
| **Validates claims** | Flags statistics and facts that don't have sources |
| **Logs everything** | Creates audit trail of what was allowed and blocked |
| **Scores compliance** | Gives you a governance "health score" (GCS) |
| **Operates independently** | Gates can run solo if siblings are unhealthy |

### What It Does NOT Do

- **Doesn't replace your AI** — Works alongside ChatGPT, Claude, Ollama, etc.
- **Doesn't send data to the cloud** — Everything runs locally on your machine
- **Doesn't require internet** — Works fully offline with local AI (Ollama)

### The Architecture

```
           KNOWLEDGE BASE
                 │
                 ▼
    ┌────────────────────────┐
    │      ACCESS GATE       │ ← Input validation + RBAC
    │      ✓ Phase 2.9.5     │   Permission checks, prompt injection defense
    └────────────────────────┘
                 │
                 ▼
              AGENT
           ┌────┴────┐
           │         │
           ▼         ▼
    ┌───────────┐  ┌───────────────────┐
    │ TOOL GATE │  │    OUTPUT GATE    │ ← What agent can DISCLOSE
    │ ✓ Nested  │  │   ✓ Phase 1-2     │   PII/secret/claim validation
    │ in Access │  └───────────────────┘
    └───────────┘            │
         │                   ▼
         ▼            Governed output
    External APIs          to you
    & Services
```

**v0.5.1 State:** Output Gate fully operational. Access Gate with nested Tool Gate operational. Content-layer RBAC enforcement planned for Phase 3.

---

## Prerequisites

### 1. Python 3.12

AgentKB requires Python 3.12 specifically.

**Check your version:**
```bash
python --version
```

**Expected:** `Python 3.12.x` (any 3.12 version works)

**Need Python 3.12?**
- **Windows:** Download from [python.org](https://www.python.org/downloads/) — choose Python 3.12.x
- **macOS:** `brew install python@3.12` (with Homebrew)
- **Linux:** `sudo apt install python3.12` (Ubuntu/Debian)

### 2. pip (Comes with Python)

Verify it works:
```bash
pip --version
```

### 3. A Terminal

- **Windows:** PowerShell (recommended) or Command Prompt
- **macOS:** Terminal
- **Linux:** Any terminal

---

## Installation

### Step 1: Download

Go to [GitHub Releases](https://github.com/j-w-code/AgentKB-public/releases) and download the wheel for your platform:

| Your Computer | Download File |
|---------------|---------------|
| Windows 64-bit | `agentkb-0.5.1-cp312-cp312-win_amd64.whl` |
| Linux 64-bit | `agentkb-0.5.1-cp312-cp312-manylinux_2_17_x86_64.whl` |
| macOS Intel | `agentkb-0.5.1-cp312-cp312-macosx_10_13_x86_64.whl` |
| macOS Apple Silicon | `agentkb-0.5.1-cp312-cp312-macosx_11_0_arm64.whl` |

### Step 2: Install

Navigate to your Downloads folder and run:

```bash
pip install agentkb-0.5.1-cp312-cp312-win_amd64.whl
```

(Replace filename with the one you downloaded)

### Step 3: Verify

```bash
agentkb --version
```

**Expected:** `agentkb 0.5.1`

**If you see "command not found":**
```bash
python -m agentkb --version
```

---

## Quick Start (5 Minutes)

Get AgentKB running in 4 commands:

```bash
# 1. Create a project folder
mkdir my-project && cd my-project

# 2. Initialize AgentKB
agentkb init

# 3. Check everything is working
agentkb doctor

# 4. Test the output gate
agentkb gate --text "My email is test@example.com"
```

**What you should see:**

```
BLOCKED (1 violations)
- sensitivity.levels.PII_INPUT_ONLY.output (high): External outputs must not include PII. Detected: EMAIL_ADDRESS

Next steps:
- Remove or generalize PII (e.g., replace with roles, not identifiers).
```

**Congratulations!** AgentKB just prevented accidental exposure of an email address.

---

## Setting Up Your LLM

AgentKB can govern AI conversations. You have two options:

### Option A: Local AI with Ollama (Recommended)

**Ollama** runs AI models directly on your computer. Free, private, works offline.

#### Install Ollama

Download from [ollama.com](https://ollama.com/download) and run the installer.

#### Download a Model

```bash
ollama pull llama3.1:8b
```

This downloads a ~4GB model. Takes 5-15 minutes.

**Smaller alternatives:**
- `ollama pull llama3.2:3b` — Faster, ~2GB
- `ollama pull phi3:mini` — Very fast, ~1.7GB

#### Verify It's Running

```bash
ollama list
```

You should see your downloaded model.

---

### Option B: Cloud AI (Anthropic, OpenAI, xAI)

Requires internet, an account, and an API key.

| Provider | Environment Variable | Get Key |
|----------|---------------------|---------|
| Anthropic (Claude) | `ANTHROPIC_API_KEY` | [console.anthropic.com](https://console.anthropic.com/) |
| OpenAI (GPT-4) | `OPENAI_API_KEY` | [platform.openai.com](https://platform.openai.com/api-keys) |
| xAI (Grok) | `XAI_API_KEY` | [console.x.ai](https://console.x.ai/) |

#### Set Your API Key

**Windows (PowerShell):**
```powershell
$env:ANTHROPIC_API_KEY = "sk-ant-your-key-here"
```

**macOS/Linux:**
```bash
export ANTHROPIC_API_KEY="sk-ant-your-key-here"
```

**Important:** Never share your API key or commit it to version control.

---

## Core Workflows

### Workflow 1: Check Text Before Sending

Use the gate to validate any text:

```bash
# Check a message
agentkb gate --text "Please contact john.smith@company.com for details"

# Check a file
agentkb gate --input draft-email.txt

# Check from clipboard/pipe
echo "Some text" | agentkb gate
```

**Possible outcomes:**
- `ALLOW` — Text is compliant, no violations
- `BLOCKED` — Violations found, with guidance on how to fix

### Workflow 2: Governed AI Chat

Have a conversation where every AI response is checked:

```bash
# With Ollama (local)
agentkb chat --ollama-model llama3.1:8b

# With Anthropic (cloud)
agentkb chat --llm anthropic --llm-model claude-3-5-sonnet-20241022

# With OpenAI (cloud)
agentkb chat --llm openai --llm-model gpt-4o
```

Type your messages. Every AI response goes through the Output Gate before you see it.

**Chat commands:**
- `/quit` — Exit the chat
- `/clear` — Clear conversation history
- `/gcs` — Show current governance compliance score

### Workflow 3: Scan Files for Sensitive Data

Find PII and secrets in your documents:

```bash
agentkb scan --path ./my-documents
```

**Output shows:**
- Files scanned
- PII found (emails, phones, SSNs) — counts only, not actual values
- Secrets found (API keys, passwords) — counts only
- File paths where issues were detected

**Note:** Scan never displays the actual sensitive content, only that it exists.

### Workflow 4: Run a Demo

See the full governance flow in action:

```bash
# With Ollama
agentkb demo --ollama-model llama3.1:8b

# Mock mode (no LLM needed, uses test responses)
agentkb demo --mock
```

The demo shows:
1. AI receives a prompt
2. AI generates a response
3. Output Gate catches violations
4. You see the governed result

### Workflow 5: Review Audit Metrics

Get aggregate statistics on governance events:

```bash
agentkb audit metrics
```

Shows blocks/day, rule coverage, event counts, and trends.

### Workflow 6: Run Adversarial Tests

Test your gate against known attack patterns:

```bash
agentkb adversarial
```

Runs encoding attacks, prompt injection attempts, and tool exfiltration tests against your gate configuration.

---

## Command Reference

### Essential Commands

| Command | What It Does | Example |
|---------|--------------|---------|
| `init` | Set up AgentKB in a folder | `agentkb init` |
| `doctor` | Check if everything is working | `agentkb doctor` |
| `gate` | Check text for violations | `agentkb gate --text "..."` |
| `chat` | AI conversation with governance | `agentkb chat --ollama-model llama3.1:8b` |
| `scan` | Find sensitive data in files | `agentkb scan --path ./docs` |
| `demo` | See governance in action | `agentkb demo --mock` |

### Monitoring Commands

| Command | What It Does | Example |
|---------|--------------|---------|
| `gcs` | Show governance compliance score | `agentkb gcs` |
| `audit` | View audit log of decisions | `agentkb audit --limit 20` |
| `audit metrics` | Aggregate audit statistics | `agentkb audit metrics` |
| `audit-bus` | Check Audit Bus health | `agentkb audit-bus status` |
| `context` | Verify session integrity | `agentkb context` |

### Testing Commands

| Command | What It Does | Example |
|---------|--------------|---------|
| `replay` | Run regression fixtures | `agentkb replay` |
| `adversarial` | Run attack pattern tests | `agentkb adversarial` |
| `bench` | Performance benchmarks | `agentkb bench` |

### Learning Commands (Tier 2 Detection)

| Command | What It Does | Example |
|---------|--------------|---------|
| `learn status` | Check semantic learning state | `agentkb learn` |
| `learn list` | Show pending specifics | `agentkb learn list` |
| `learn confirm` | Confirm a detection | `agentkb learn confirm <class_id> <hash>` |
| `learn reject` | Reject false positive | `agentkb learn reject <class_id> <hash>` |

### Gate Options

```bash
# Basic text check
agentkb gate --text "Your message here"

# Check a file
agentkb gate --input document.txt

# Get JSON output (for scripts/automation)
agentkb gate --text "..." --format json

# Auto-repair: try to fix violations automatically
agentkb gate --text "..." --repair --max-retries 2

# Dry-run: check without logging
agentkb gate --text "..." --no-log
```

### Audit Options

```bash
# Recent events
agentkb audit --limit 50

# Only blocked items
agentkb audit --decision block

# Time range
agentkb audit --start 2026-01-25T00:00:00Z

# Export to JSON
agentkb audit --format json > audit-report.json

# Aggregate metrics
agentkb audit metrics --format json
```

---

## Understanding Detection

AgentKB uses a **3-tier detection system** to catch sensitive content:

### Tier 1: Pattern Detection (Instant)

Catches obvious patterns using Presidio (Microsoft's PII detection library):

| Pattern | Example |
|---------|---------|
| Email addresses | `john@example.com` |
| Phone numbers | `(555) 123-4567` |
| Social Security Numbers | `123-45-6789` |
| Credit card numbers | `4111-1111-1111-1111` |
| IP addresses | `192.168.1.1` |

**Speed:** ~1-5ms

### Tier 2: Semantic Detection (Fast)

Catches variations and context-aware patterns using AI embeddings:

| What It Catches | Example |
|-----------------|---------|
| Disguised PII | `john [at] example [dot] com` |
| Named entities in context | `Contact our CEO John Smith` |
| Secret patterns | `the password is: hunter2` |
| Verbal descriptions | `My social is one two three...` |

**Speed:** ~10-50ms

**22 Reference Classes:** SSN verbal, email obfuscated, phone verbal, address partial, DOB verbal, name relationship, ID partial, medical verbal, card partial, bank verbal, crypto address, income disclosure, API key verbal, password hint, key prefix, connection string, token describe, MFA backup, employer detail, location precise, schedule pattern, travel plan.

### Tier 3: LLM Evaluation (Optional)

For novel cases, can use an LLM to evaluate if content violates governance.

**Speed:** ~100-500ms (only used when needed)

### Detection Output Explained

When something is blocked, you'll see:

```
BLOCKED (1 violations)
- sensitivity.levels.PII_INPUT_ONLY.output (high): External outputs must not include PII. Detected: EMAIL_ADDRESS(conf=0.95)

Next steps:
- Remove or generalize PII (e.g., replace with roles, not identifiers).
```

**Breaking this down:**
- `sensitivity.levels.PII_INPUT_ONLY.output` — The rule that was violated
- `(high)` — Severity level (low/medium/high/critical)
- `EMAIL_ADDRESS(conf=0.95)` — What was detected and confidence (0-1)
- `Next steps` — Actionable guidance

---

## Operational Modes

AgentKB v0.5.1 introduces **four operational modes** for gate resilience. Gates can operate independently when siblings are unhealthy.

### FULL Mode

Both Access Gate and Output Gate are healthy and can cross-verify.

```
$ agentkb doctor
Gates: OK (mode=full)
```

- Complete cross-verification via Audit Bus
- Provenance tracking between gates
- Normal operation

### SOLO-OG Mode (Output Gate Solo)

Output Gate operates independently when Access Gate is unhealthy.

```
$ agentkb doctor
Gates: DEGRADED (mode=solo-og, degraded=[access_gate])
```

- Output Gate runs full 3-tier detection
- Events include `degraded_flag: true`
- User experience: Normal output, degraded state logged

### SOLO-AG Mode (Access Gate Solo)

Access Gate operates independently when Output Gate is unhealthy.

```
$ agentkb doctor
Gates: DEGRADED (mode=solo-ag, degraded=[output_gate])
```

- Access Gate validates input, grants access
- Tool invocation checks still active
- User experience: May see degraded flag warnings

### ISLAND Mode

Both gates healthy but cannot coordinate (network partition).

- Both gates operate solo
- Both perspectives captured for post-incident reconstruction
- Rare edge case

### Default Behavior

AgentKB uses **SOLO_FALLBACK** by default: availability with visibility over strict fail-closed. Gates continue operating when siblings are unhealthy, with degraded state logged for audit.

For strict environments requiring fail-closed on any gate failure, this can be configured programmatically.

---

## Monitoring & Compliance

### Governance Compliance Score (GCS)

GCS is a 0-100 score measuring governance health:

```bash
agentkb gcs
```

**Output:**
```
GCS: 100/100
Components:
  - Governance loaded: ✓
  - Session valid: ✓
  - No critical violations: ✓
  - Audit logging active: ✓
```

| Score | Meaning |
|-------|---------|
| 100 | Full compliance — everything working correctly |
| 80-99 | Good — minor issues, system functional |
| 50-79 | Warning — some governance gaps |
| <50 | Critical — governance not enforced |

### Audit Logs

Every decision is logged. View your audit trail:

```bash
# Recent activity
agentkb audit --limit 10

# Only blocked items
agentkb audit --decision block

# Today's activity
agentkb audit --start 2026-01-27T00:00:00Z

# Aggregate metrics
agentkb audit metrics
```

**Where are logs stored?**
- `.agentkb/derived/audit_events.jsonl` — Machine-readable log
- `.agentkb/derived/error_events.jsonl` — Violations and errors

### Audit Metrics (v0.5.1)

Get aggregate statistics:

```bash
agentkb audit metrics
```

Shows:
- Blocks per day
- Rule coverage (which rules triggered)
- Event counts by type
- Trends over time

### Context Integrity

Verify your session hasn't drifted:

```bash
agentkb context
```

This checks:
- Governance version matches what was loaded at session start
- No unexpected changes to configuration
- Time anchor is valid

### Gate Health

Check operational mode and gate status:

```bash
agentkb doctor
```

Shows:
- Governance status
- Gate health (access_gate, output_gate)
- Operational mode (full, solo-og, solo-ag, island)
- Degraded gates if any

---

## Expected Behavior

### What "Working Correctly" Looks Like

**1. Doctor shows READY with gate status:**
```
$ agentkb doctor
AgentKB Doctor
==============
Governance: OK (v0.3.7)
Derived dir: OK (writable)
Gates: OK (mode=full)
Status: READY
```

**2. Clean text passes through:**
```
$ agentkb gate --text "The quarterly report shows 15% growth."
ALLOW
The quarterly report shows 15% growth.
```

**3. PII is blocked with guidance:**
```
$ agentkb gate --text "Contact john.doe@company.com for details"
BLOCKED (1 violations)
- sensitivity.levels.PII_INPUT_ONLY.output (high): External outputs must not include PII. Detected: EMAIL_ADDRESS(conf=1.00)

Next steps:
- Remove or generalize PII (e.g., replace with roles, not identifiers).
```

**4. Secrets trigger critical blocks:**
```
$ agentkb gate --text "API key: sk-abc123def456"
BLOCKED (1 violations)
- sensitivity.levels.SECRET.output (critical): External outputs must never include SECRET data. Detected: SECRET_KEY(conf=0.90)
```

**5. Unsourced claims are flagged:**
```
$ agentkb gate --text "Revenue increased by exactly $5.2 million."
BLOCKED (1 violations)
- claims.output_tiers.external.require_evidence (high): EXTERNAL outputs require evidence for DERIVED claims.

Next steps:
- Add source citation or rephrase as estimate/opinion.
```

**6. GCS shows full compliance when healthy:**
```
$ agentkb gcs
GCS: 100/100
```

**7. Degraded mode is visible when gates are unhealthy:**
```
$ agentkb doctor
Gates: DEGRADED (mode=solo-og, degraded=[access_gate])
```

---

## Troubleshooting

### Installation Problems

| Problem | Cause | Fix |
|---------|-------|-----|
| `pip install` fails with "wheel not supported" | Wrong Python version or platform | Check `python --version` is 3.12.x; download correct wheel |
| `agentkb: command not found` | Scripts folder not in PATH | Use `python -m agentkb` instead |
| Import errors after install | Corrupted installation | `pip uninstall agentkb` then reinstall |

### Initialization Problems

| Problem | Cause | Fix |
|---------|-------|-----|
| `Governance load failed` | Missing `.agentkb/` folder | Run `agentkb init` |
| `Permission denied` | No write access to folder | Check folder permissions |
| Config files corrupted | Manual edits broke YAML | Run `agentkb init --force` to reset |

### Ollama Problems

| Problem | Cause | Fix |
|---------|-------|-----|
| "Failed to connect to Ollama" | Ollama not running | Windows: Check system tray. macOS/Linux: Run `ollama serve` |
| "Model not found" | Model not downloaded | Run `ollama pull llama3.1:8b` |
| Very slow responses | Model too large for hardware | Try `ollama pull llama3.2:3b` or `phi3:mini` |
| Out of memory | Not enough RAM | Use smaller model or close other apps |

### API Key Problems

| Problem | Cause | Fix |
|---------|-------|-----|
| "API_KEY not set" | Environment variable missing | Set it: `$env:ANTHROPIC_API_KEY = "..."` (PowerShell) |
| "Invalid API key" | Wrong/expired key | Generate new key from provider console |
| "Rate limited" | Too many requests | Wait a few minutes, or upgrade API plan |

### Gate Problems

| Problem | Cause | Fix |
|---------|-------|-----|
| Blocks everything | Overly strict config (rare) | Check violation details; contact support |
| Allows everything | Governance not loaded | Run `agentkb doctor` to verify |
| Wrong detections | Detection false positive | Use `--format json` for details; report issue |
| Slow response | Large text input | Split into smaller chunks |
| Degraded mode | Sibling gate unhealthy | Check `agentkb doctor` for details |

### GCS Problems

| Problem | Cause | Fix |
|---------|-------|-----|
| GCS below 100 | Governance issues | Run `agentkb doctor` for details |
| GCS shows 0 | Governance not initialized | Run `agentkb init` |
| Can't query GCS | No audit data | Run some gate commands first |

### Common Error Messages

| Error | Meaning | Fix |
|-------|---------|-----|
| `Governance load failed` | No `.agentkb/governance.yaml` | Run `agentkb init` |
| `Unknown role` | Role not defined | Use `reader` (default) or check `.agentkb/roles.yaml` |
| `LLM provider error` | API call failed | Check API key and internet |
| `Session expired` | Chat session timed out | Start new `agentkb chat` |
| `Context integrity violation` | Config changed mid-session | Restart your session |
| `Gate unhealthy` | Sibling gate not responding | Check logs; system continues in solo mode |

---

## Glossary

| Term | Meaning |
|------|---------|
| **Access Gate** | Input validation + RBAC filter controlling what agent can read |
| **Agent** | An AI system that performs tasks (ChatGPT, Claude, etc.) |
| **API Key** | Secret credential for accessing cloud AI services |
| **Audit Bus** | Event coordination layer for gate-to-gate communication |
| **Audit Log** | Record of all gate decisions for compliance |
| **Degraded Mode** | Gate operating independently when sibling is unhealthy |
| **GCS** | Governance Compliance Score — 0-100 health metric |
| **Governance** | Structural rules defining what AI can and cannot output |
| **Gov-Compliant** | Text that passes all governance rules |
| **Gov-Noncompliant** | Text that violates one or more governance rules |
| **LLM** | Large Language Model — the AI technology behind ChatGPT, Claude |
| **Ollama** | Software that runs AI models locally on your computer |
| **Operational Mode** | Current gate coordination state (full, solo-og, solo-ag, island) |
| **Output Gate** | AgentKB's filter that checks responses for violations |
| **PII** | Personally Identifiable Information — emails, SSNs, phone numbers |
| **Presidio** | Microsoft's open-source PII detection library (used in Tier 1) |
| **RBAC** | Role-Based Access Control — different permissions per role |
| **Redaction** | Replacing sensitive text with placeholders like `<REDACTED>` |
| **Semantic Detection** | AI-powered detection that understands meaning, not just patterns |
| **SOLO_FALLBACK** | Default behavior where gates continue operating when siblings fail |
| **Tool Gate** | Filter controlling agent tool invocations (nested in Access Gate) |
| **Violation** | When text breaks a governance rule |
| **Wheel** | Pre-built Python package file (`.whl` extension) |

---

## Getting Help

- **GitHub Issues:** [github.com/j-w-code/AgentKB-public/issues](https://github.com/j-w-code/AgentKB-public/issues)
- **Email:** agentkb_jw@proton.me

---

*© 2025-2026 J.W. All rights reserved.*
