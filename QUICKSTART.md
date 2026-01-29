# AgentKB Quick Start Guide

**Time to first demo: ~10 minutes**

This guide gets you from zero to running AgentKB. No prior Python or programming experience required.

---

## What You'll Need

- A computer (Windows, Mac, or Linux)
- Internet connection (for downloads)
- ~500MB disk space

---

## Step 1: Install Python

AgentKB requires Python 3.10 or newer. Skip this step if you already have Python installed.

### Check if Python is Already Installed

Open a terminal:
- **Windows**: Press `Win + R`, type `cmd`, press Enter
- **Mac**: Press `Cmd + Space`, type `Terminal`, press Enter
- **Linux**: Press `Ctrl + Alt + T`

Type this and press Enter:
```
python --version
```

If you see `Python 3.10` or higher (e.g., `Python 3.12.0`), skip to Step 2.

If you see "command not found" or a version below 3.10, continue below.

### Installing Python

**Windows:**
1. Go to [python.org/downloads](https://www.python.org/downloads/)
2. Click the big "Download Python 3.12.x" button
3. Run the installer
4. **IMPORTANT**: Check the box that says "Add Python to PATH" before clicking Install
5. Click "Install Now"

**Mac:**
1. Go to [python.org/downloads](https://www.python.org/downloads/)
2. Click the big "Download Python 3.12.x" button
3. Open the downloaded `.pkg` file
4. Follow the installer prompts

**Linux (Ubuntu/Debian):**
```
sudo apt update
sudo apt install python3.12 python3-pip
```

After installing, close and reopen your terminal, then verify:
```
python --version
```

> **Troubleshooting**: On some systems, you may need to use `python3` instead of `python`.

---

## Step 2: Install AgentKB

### Option A: From Pre-Built Wheel (Recommended)

1. Go to the [Releases page](https://github.com/j-w-code/AgentKB-public/releases)
2. Download the wheel file for your system:
   - Windows: `agentkb-*-win_amd64.whl`
   - Mac (Intel): `agentkb-*-macosx_x86_64.whl`
   - Mac (Apple Silicon): `agentkb-*-macosx_arm64.whl`
   - Linux: `agentkb-*-manylinux*.whl`

3. Open a terminal and navigate to your Downloads folder:

   **Windows:**
   ```
   cd %USERPROFILE%\Downloads
   ```

   **Mac/Linux:**
   ```
   cd ~/Downloads
   ```

4. Install the wheel (replace the filename with your downloaded file):
   ```
   pip install agentkb-0.5.2-cp312-cp312-win_amd64.whl
   ```

### Option B: From PyPI

```
pip install agentkb
```

### Verify Installation

```
agentkb --version
```

You should see something like: `agentkb 0.5.2`

> **Troubleshooting**: If you get "agentkb not found", try:
> - Windows: Close and reopen your terminal
> - Mac/Linux: Run `pip3 install agentkb` instead

---

## Step 3: Initialize a Workspace

AgentKB needs a folder to store its configuration. Let's create one:

1. Create a folder for testing (you can use any location):

   **Windows:**
   ```
   mkdir %USERPROFILE%\agentkb-demo
   cd %USERPROFILE%\agentkb-demo
   ```

   **Mac/Linux:**
   ```
   mkdir ~/agentkb-demo
   cd ~/agentkb-demo
   ```

2. Initialize AgentKB:
   ```
   agentkb init
   ```

   You should see:
   ```
   AgentKB initialized.
   Created: .agentkb/governance.yaml
   Created: .agentkb/roles.yaml
   ...
   ```

3. Verify everything is working:
   ```
   agentkb doctor
   ```

   You should see all checks passing:
   ```
   Governance:  OK
   Derived Dir: OK
   ...
   ```

🎉 **AgentKB is now ready to use!**

---

## Step 4: Try the Output Gate (No LLM Required)

The Output Gate is AgentKB's core feature — it scans text and blocks sensitive content.

### Test 1: Safe Text (Allowed)

```
agentkb gate --text "Hello! The weather is nice today."
```

Output:
```
Decision: ALLOW
Output: Hello! The weather is nice today.
```

### Test 2: PII Detection (Blocked)

```
agentkb gate --text "Contact me at john.doe@example.com or call 555-123-4567"
```

Output:
```
Decision: BLOCK
Violations:
  - PII detected: email address
  - PII detected: phone number
Output: Contact me at <REDACTED:PII> or call <REDACTED:PII>
```

### Test 3: Secret Detection (Blocked)

```
agentkb gate --text "Use API key sk-abc123xyz789 to authenticate"
```

Output:
```
Decision: BLOCK
Violations:
  - SECRET detected: API key pattern
Output: Use API key <REDACTED:SECRET> to authenticate
```

### Test 4: Unsourced Claim (Blocked)

```
agentkb gate --text "Our product increased sales by 47% last quarter."
```

Output:
```
Decision: BLOCK
Violations:
  - FACT claim requires evidence
Next steps: Add citation (e.g., "Source: Q4 report")
```

### Test 5: Sourced Claim (Allowed)

```
agentkb gate --text "Our product increased sales by 47% last quarter (Source: Q4 Sales Report, p.12)"
```

Output:
```
Decision: ALLOW
Output: Our product increased sales by 47% last quarter (Source: Q4 Sales Report, p.12)
```

---

## Step 5: (Optional) Add LLM Support with Ollama

To use AgentKB's interactive chat and demo modes, you need a local LLM. We recommend Ollama — it's free and runs entirely on your computer.

### Install Ollama

1. Go to [ollama.ai](https://ollama.ai)
2. Click "Download" and install for your system
3. Open a **new terminal** and run:
   ```
   ollama pull llama3.2:3b
   ```
   This downloads a small, capable model (~2GB). Wait for it to complete.

### Verify Ollama is Running

```
agentkb doctor --llm ollama
```

You should see:
```
Ollama: OK (model: llama3.2:3b available)
```

### Run the Demo

The demo shows AgentKB's governance in action:

```
agentkb demo --ollama-model llama3.2:3b
```

Watch as:
1. The LLM receives a prompt containing SECRET data
2. The LLM generates a response that includes the secret
3. **AgentKB's Output Gate blocks the secret from being disclosed**
4. A "repaired" response is generated without the sensitive content

### Interactive Chat

Start a governed chat session:

```
agentkb chat --ollama-model llama3.2:3b
```

Commands:
- Type messages normally to chat
- Type `/help` for commands
- Type `/quit` to exit

Every response is scanned by the Output Gate before you see it.

---

## What's Next?

You've just:
- ✅ Installed AgentKB
- ✅ Tested the Output Gate on text
- ✅ (Optionally) Set up a local LLM for interactive demos

### Learn More

| Guide | What It Covers |
|-------|----------------|
| [USER_GUIDE.md](USER_GUIDE.md) | All CLI commands and options |
| [API_REFERENCE.md](API_REFERENCE.md) | REST API for integrations |
| [ARCHITECTURE.md](ARCHITECTURE.md) | How AgentKB works under the hood |
| [PHILOSOPHY.md](PHILOSOPHY.md) | Why "Agents = Principals" matters |

### Common Commands Reference

| Command | What It Does |
|---------|--------------|
| `agentkb gate --text "..."` | Check text through the output gate |
| `agentkb gate --input file.txt` | Check a file's contents |
| `agentkb scan --path ./folder` | Scan a folder for PII/secrets |
| `agentkb chat --ollama-model X` | Interactive chat with governance |
| `agentkb demo --ollama-model X` | Run the full demo |
| `agentkb doctor` | Check system health |

### Getting Help

- **Issues?** Check the [Troubleshooting](#troubleshooting) section in USER_GUIDE.md
- **Questions?** Email agentkb_jw@proton.me

---

## Troubleshooting

### "python not found" or "pip not found"

- Make sure you checked "Add Python to PATH" during installation
- Try using `python3` and `pip3` instead
- Windows: Close and reopen your terminal after installing Python

### "agentkb not found"

- Close and reopen your terminal
- Try: `python -m agentkb --version`
- Reinstall: `pip install --force-reinstall agentkb`

### "ollama: command not found"

- Make sure Ollama is installed from [ollama.ai](https://ollama.ai)
- Mac: Run the Ollama app from Applications first
- Try opening a new terminal window

### Ollama model download is slow

- The `llama3.2:3b` model is ~2GB — downloading may take 5-15 minutes depending on your connection
- You can use a smaller model: `ollama pull phi3:mini` (~1GB)

### Gate blocks everything

- Check for typos in your input
- Use `--format json` to see detailed violation info:
  ```
  agentkb gate --text "your text" --format json
  ```

---

*Built for a world where AI agents are principals, not just tools.*
