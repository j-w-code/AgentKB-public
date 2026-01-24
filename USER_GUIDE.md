# AgentKB User Guide

**For complete beginners.** This guide assumes no prior experience with Python packages, AI APIs, or AgentKB.

---

## Table of Contents

1. [What is AgentKB?](#what-is-agentkb)
2. [Prerequisites](#prerequisites)
3. [Installation](#installation)
4. [Setting Up Your LLM](#setting-up-your-llm)
5. [Your First Commands](#your-first-commands)
6. [Command Reference](#command-reference)
7. [Expected Behavior](#expected-behavior)
8. [Troubleshooting](#troubleshooting)
9. [Glossary](#glossary)

---

## What is AgentKB?

AgentKB is a **governance layer** for AI agents. It sits between your AI assistant and the outside world, ensuring that:

- **Sensitive data doesn't leak** — PII (personal information), passwords, API keys are blocked
- **Claims have evidence** — The AI can't make up statistics or facts without sources
- **Everything is logged** — You can see what the AI tried to say and what was blocked

Think of it as a filter that reviews everything an AI writes before it reaches you or anyone else.

### What AgentKB Does NOT Do

- It doesn't replace your AI (ChatGPT, Claude, etc.) — it works alongside it
- It doesn't store your data in the cloud — everything runs locally
- It doesn't require an internet connection (if using local AI)

---

## Prerequisites

Before installing AgentKB, you need:

### 1. Python 3.12

AgentKB requires Python 3.12 specifically.

**Check if you have Python:**
```bash
python --version
```

**Expected output:** `Python 3.12.x` (any version starting with 3.12)

**If you don't have Python 3.12:**
- **Windows:** Download from [python.org](https://www.python.org/downloads/) — choose Python 3.12.x
- **macOS:** `brew install python@3.12` (if you have Homebrew)
- **Linux:** `sudo apt install python3.12` (Ubuntu/Debian)

### 2. pip (Python Package Installer)

pip comes with Python. Verify it works:

```bash
pip --version
```

**Expected output:** Something like `pip 24.x from ...`

### 3. A Terminal/Command Line

- **Windows:** PowerShell (recommended) or Command Prompt
- **macOS:** Terminal
- **Linux:** Any terminal emulator

---

## Installation

AgentKB is distributed as a "wheel" file — a pre-built Python package.

### Step 1: Download the Wheel

Go to [GitHub Releases](https://github.com/j-w-code/AgentKB-public/releases) and download the wheel for your platform:

| Your Computer | Download File |
|---------------|---------------|
| Windows 64-bit | `agentkb-0.4.10-cp312-cp312-win_amd64.whl` |
| Linux 64-bit | `agentkb-0.4.10-cp312-cp312-manylinux_2_17_x86_64.whl` |
| macOS Intel | `agentkb-0.4.10-cp312-cp312-macosx_10_13_x86_64.whl` |
| macOS Apple Silicon | `agentkb-0.4.10-cp312-cp312-macosx_11_0_arm64.whl` |

### Step 2: Install the Wheel

Open your terminal, navigate to where you downloaded the file, and run:

```bash
pip install agentkb-0.4.10-cp312-cp312-win_amd64.whl
```

(Replace the filename with the one you downloaded)

**What does this do?** It installs AgentKB and all its dependencies on your computer.

### Step 3: Verify Installation

```bash
agentkb --version
```

**Expected output:** `agentkb 0.4.10` (or similar)

If you see `command not found`, try:
```bash
python -m agentkb --version
```

---

## Setting Up Your LLM

AgentKB works with AI language models (LLMs) to demonstrate governance. You have two options:

### Option A: Local AI with Ollama (Free, Private, No Internet Required)

**Ollama** runs AI models directly on your computer. Nothing leaves your machine.

#### Step 1: Install Ollama

- **Windows/macOS/Linux:** Download from [ollama.com](https://ollama.com/download)
- Run the installer

#### Step 2: Download a Model

Open a new terminal and run:

```bash
ollama pull llama3.1:8b
```

**What does this do?** Downloads a ~4GB AI model to your computer. This takes 5-15 minutes depending on your internet speed.

**Alternative smaller models:**
- `ollama pull llama3.2:3b` — Faster, less capable (~2GB)
- `ollama pull phi3:mini` — Very fast, basic (~1.7GB)

#### Step 3: Verify Ollama is Running

```bash
ollama list
```

**Expected output:** A list showing the model you downloaded.

**Note:** Ollama runs as a background service. After installing, it should start automatically.

---

### Option B: Cloud AI with API Keys (Requires Internet + Account)

You can use cloud AI providers. This requires:
1. An account with the provider
2. An API key (like a password for the API)
3. Setting an environment variable

#### Supported Providers

| Provider | Environment Variable | Get API Key |
|----------|---------------------|-------------|
| Anthropic (Claude) | `ANTHROPIC_API_KEY` | [console.anthropic.com](https://console.anthropic.com/) |
| OpenAI (GPT-4) | `OPENAI_API_KEY` | [platform.openai.com](https://platform.openai.com/api-keys) |
| xAI (Grok) | `XAI_API_KEY` | [console.x.ai](https://console.x.ai/) |

#### Setting Your API Key

**Windows (PowerShell):**
```powershell
$env:ANTHROPIC_API_KEY = "sk-ant-your-key-here"
```

**macOS/Linux:**
```bash
export ANTHROPIC_API_KEY="sk-ant-your-key-here"
```

**Important:** This only lasts for your current terminal session. For permanent setup, add the line to your shell profile (`.bashrc`, `.zshrc`, or PowerShell profile).

**Never share your API key or commit it to version control.**

---

## Your First Commands

### Step 1: Initialize a Workspace

Create a folder for your project and initialize AgentKB:

```bash
mkdir my-project
cd my-project
agentkb init
```

**What does this do?** Creates a `.agentkb/` folder with governance configuration files.

**Expected output:**
```
Initialized AgentKB workspace at /path/to/my-project/.agentkb
Created: .agentkb/governance.yaml, .agentkb/roles.yaml, .agentkb/classification_map.yaml, .agentkb/.gitignore, .agentkb/fixtures/historical_failures.jsonl

Next steps:
  1. Run: agentkb doctor
  2. Customize .agentkb/governance.yaml for your needs
  3. Run: agentkb gate --text 'test message'
```

### Step 2: Run Diagnostics

```bash
agentkb doctor
```

**What does this do?** Checks that everything is set up correctly.

**Expected output:**
```
AgentKB Doctor
==============
Governance: OK (v0.3.0)
Derived dir: OK (writable)
LLM: OK (llama3.1:8b)   ← Only shows if Ollama is running
Status: READY
```

### Step 3: Test the Output Gate

The output gate is AgentKB's core feature — it checks text for policy violations.

```bash
agentkb gate --text "Hello, this is a test message."
```

**Expected output:**
```
ALLOW
Hello, this is a test message.
```

Now try something that should be blocked:

```bash
agentkb gate --text "My SSN is 123-45-6789 and my email is john@example.com"
```

**Expected output:**
```
BLOCKED (1 violations)
- sensitivity.levels.PII_INPUT_ONLY.output (high): External outputs must not include PII. EMAIL_ADDRESS(conf=1.00), US_SSN(conf=0.85)

Next steps:
- Remove or generalize PII (e.g., replace with roles, not identifiers).
- If disclosure is required, obtain explicit user approval (not supported in this MVP).
```

The gate caught the sensitive information and provides guidance on how to fix it.

### Step 4: Have a Gated Conversation (Optional — Requires LLM)

If you set up Ollama or an API key:

**With Ollama:**
```bash
agentkb chat --ollama-model llama3.1:8b
```

**With Anthropic:**
```bash
agentkb chat --llm anthropic --llm-model claude-3-5-sonnet-20241022
```

**What does this do?** Opens an interactive chat where every AI response goes through the governance gate before you see it.

Type `/quit` to exit.

---

## Command Reference

### Core Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `agentkb init` | Initialize workspace | `agentkb init` |
| `agentkb doctor` | Check setup health | `agentkb doctor` |
| `agentkb gate` | Check text for violations | `agentkb gate --text "..."` |
| `agentkb chat` | Interactive gated chat | `agentkb chat --ollama-model llama3.1:8b` |
| `agentkb scan` | Scan files for PII/secrets | `agentkb scan --path ./documents` |

### Gate Command Options

```bash
# Check text directly
agentkb gate --text "Your text here"

# Check a file
agentkb gate --input draft.txt

# Pipe from another command
cat document.txt | agentkb gate

# Get JSON output (for scripts)
agentkb gate --text "..." --format json

# Try to auto-repair blocked content
agentkb gate --text "..." --repair --max-retries 2
```

### Chat Command Options

```bash
# Local AI (Ollama)
agentkb chat --ollama-model llama3.1:8b

# Cloud AI providers
agentkb chat --llm anthropic --llm-model claude-3-5-sonnet-20241022
agentkb chat --llm openai --llm-model gpt-4o
agentkb chat --llm xai --llm-model grok-2

# With auto-repair on blocked responses
agentkb chat --ollama-model llama3.1:8b --repair --max-retries 2
```

### Scan Command

Scan a folder for potential sensitive data:

```bash
agentkb scan --path ./my-documents
```

**Output shows:**
- Number of files scanned
- PII patterns found (emails, phones, SSNs)
- Secret patterns found (API keys, passwords)
- Paths only — never shows actual content

### Audit and Compliance Commands

```bash
# Check governance compliance score
agentkb gcs

# Query audit logs (filter by time, action, decision)
agentkb audit --limit 50
agentkb audit --action gate --decision block
agentkb audit --start 2026-01-23T00:00:00Z

# Verify context integrity
agentkb context
```

---

## Expected Behavior

### What "Working Correctly" Looks Like

1. **`agentkb doctor` shows "Status: READY"** — No errors

2. **Safe text passes through unchanged:**
   ```
   $ agentkb gate --text "The quarterly report shows 15% growth."
   ALLOW
   The quarterly report shows 15% growth.
   ```

3. **PII gets blocked with guidance:**
   ```
   $ agentkb gate --text "Contact me at john.doe@company.com"
   BLOCKED (1 violations)
   - sensitivity.levels.PII_INPUT_ONLY.output (high): External outputs must not include PII...
   ```

4. **Secrets trigger critical violations:**
   ```
   $ agentkb gate --text "My API key is sk-abc123..."
   BLOCKED (1 violations)
   - sensitivity.levels.SECRET.output (critical): External outputs must never include SECRET data.
   ```

5. **Unsourced factual claims are flagged:**
   ```
   $ agentkb gate --text "Sales increased by exactly $5.2 million this quarter."
   BLOCKED (1 violations)
   - claims.output_tiers.external.require_evidence (high): EXTERNAL outputs require evidence for DERIVED claims.
   ```

6. **Chat responses are filtered before you see them:**
   - If the AI tries to output something sensitive, you see the redacted version
   - Blocked attempts are logged to `.agentkb/derived/error_events.jsonl`

---

## Troubleshooting

### Installation Issues

**Problem:** `pip install` fails with "wheel not supported"
- **Cause:** Wrong Python version or wrong platform wheel
- **Fix:** Verify `python --version` shows 3.12.x; download correct wheel for your OS

**Problem:** `agentkb: command not found`
- **Cause:** Python scripts folder not in PATH
- **Fix:** Try `python -m agentkb` instead, or add Python's Scripts folder to your PATH

### Ollama Issues

**Problem:** `agentkb chat` says "Failed to connect to Ollama"
- **Cause:** Ollama service not running
- **Fix:** 
  - Windows: Check that Ollama is running in system tray
  - macOS/Linux: Run `ollama serve` in a separate terminal

**Problem:** "Model not found"
- **Cause:** Model not downloaded
- **Fix:** Run `ollama pull llama3.1:8b` (or your chosen model)

**Problem:** Very slow responses
- **Cause:** Model too large for your hardware
- **Fix:** Try a smaller model: `ollama pull llama3.2:3b` or `phi3:mini`

### API Key Issues

**Problem:** "ANTHROPIC_API_KEY not set" (or similar)
- **Cause:** Environment variable not set
- **Fix:** Set the variable in your current terminal session (see [Setting Your API Key](#setting-your-api-key))

**Problem:** "Invalid API key" or "Authentication failed"
- **Cause:** Wrong key, expired key, or key copied incorrectly
- **Fix:** 
  - Generate a new key from the provider's console
  - Make sure you copied the entire key (no extra spaces)
  - Check the key hasn't expired

### Gate Issues

**Problem:** Gate blocks everything / too many false positives
- **Cause:** Overly aggressive detection (rare in v0.4.10+)
- **Fix:** 
  - Check the specific violation reason in the output
  - Use `--format json` for detailed violation info
  - Contact us if patterns seem wrong

**Problem:** Gate allows everything / nothing blocked
- **Cause:** Governance files missing or corrupted
- **Fix:** 
  - Run `agentkb doctor` to check status
  - Re-run `agentkb init --force` to regenerate config files

### Common Error Messages

| Error | Meaning | Fix |
|-------|---------|-----|
| `Governance load failed` | Missing `.agentkb/governance.yaml` | Run `agentkb init` |
| `Unknown role` | Role name not in `roles.yaml` | Use default `reader` role or check spelling |
| `LLM provider error` | API request failed | Check API key and internet connection |
| `Session expired` | Chat session timed out | Start a new `agentkb chat` session |

---

## Glossary

| Term | Meaning |
|------|---------|
| **Agent** | An AI system that performs tasks (like ChatGPT, Claude, etc.) |
| **API Key** | A secret password that lets your code talk to cloud AI services |
| **Governance** | Rules about what AI can and cannot do |
| **LLM** | Large Language Model — the AI technology behind ChatGPT, Claude, etc. |
| **Ollama** | Software that runs AI models locally on your computer |
| **Output Gate** | AgentKB's filter that checks AI responses for policy violations |
| **PII** | Personally Identifiable Information — names, emails, SSNs, phone numbers |
| **RBAC** | Role-Based Access Control — different users/agents have different permissions |
| **Redaction** | Replacing sensitive text with placeholders like `<REDACTED:PII>` |
| **Wheel** | A pre-built Python package file (`.whl` extension) |

---

## Getting Help

- **GitHub Issues:** [github.com/j-w-code/AgentKB-public/issues](https://github.com/j-w-code/AgentKB-public/issues)
- **Email:** agentkb_jw@proton.me

---

*© 2025-2026 J.W. All rights reserved.*
