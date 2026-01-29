# MCP Integration Guide

AgentKB provides a **Model Context Protocol (MCP)** server to expose governance tools to MCP-compatible clients (Claude Desktop, Cursor, VS Code extensions, etc.).

---

## What is MCP?

MCP (Model Context Protocol) is an open standard for connecting AI assistants to external tools and data sources. AgentKB's MCP server allows any MCP-compatible client to:
- Validate text against governance rules
- Check system health
- Retrieve governance configuration

---

## Install

```bash
pip install agentkb[mcp]
```

Or install all optional dependencies:

```bash
pip install agentkb[all]
```

---

## Quick Start

### Stdio Transport (Recommended)

Most MCP clients use stdio transport for local tools:

```bash
python -m agentkb.server.mcp --transport stdio
```

### HTTP Transport

For remote or shared deployments:

```bash
python -m agentkb.server.mcp --transport streamable-http
```

**Note:** HTTP transport binds to `http://localhost:8000/mcp` by default.

---

## Available Tools

### `agentkb_gate`

Validates text against governance rules (Output Gate).

**Input:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `text` | string | ✓ | Text to validate |
| `output_tier` | string | | `external`, `internal`, or `strategic` (default: `external`) |
| `repo_root` | string | | Repository root path (auto-detected if not provided) |

**Output:**
```json
{
  "decision": "allow",
  "violations": [],
  "output": "The validated text..."
}
```

**Example Usage (in MCP client):**
```
Use agentkb_gate to check: "Our revenue grew 50% last quarter"
```

### `agentkb_doctor`

Preflight health check — verifies governance loads and system is ready.

**Input:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `repo_root` | string | | Repository root path |

**Output:**
```json
{
  "ok": true,
  "checks": [
    {"name": "governance_load", "status": "ok"},
    {"name": "derived_dir_writable", "status": "ok"}
  ]
}
```

### `agentkb_get_governance`

Retrieves current governance configuration.

**Input:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `repo_root` | string | | Repository root path |

**Output:**
```json
{
  "codex_version": "0.3.9",
  "sha256": "abc123...",
  "rules": {
    "claims": {...},
    "sensitivity": {...},
    "temporal": {...}
  }
}
```

---

## Available Prompt

### `agentkb_governance_context`

A prompt template that injects governance constraints into the assistant's system context.

**Usage:** Configure your MCP client to use this prompt to make the assistant governance-aware.

---

## Client Configuration

### Claude Desktop

Add to your Claude Desktop MCP config (`~/Library/Application Support/Claude/claude_desktop_config.json` on macOS):

```json
{
  "mcpServers": {
    "agentkb": {
      "command": "python",
      "args": ["-m", "agentkb.server.mcp", "--transport", "stdio"]
    }
  }
}
```

### Cursor

Add to your Cursor settings:

```json
{
  "mcp.servers": {
    "agentkb": {
      "command": "python",
      "args": ["-m", "agentkb.server.mcp", "--transport", "stdio"]
    }
  }
}
```

### Custom Clients

For custom MCP clients, connect to the stdio or HTTP transport:

**Stdio:**
```python
import subprocess
proc = subprocess.Popen(
    ["python", "-m", "agentkb.server.mcp", "--transport", "stdio"],
    stdin=subprocess.PIPE,
    stdout=subprocess.PIPE
)
# Send/receive MCP messages via proc.stdin/stdout
```

**HTTP:**
```python
import httpx
response = httpx.post(
    "http://localhost:8000/mcp",
    json={"method": "tools/call", "params": {"name": "agentkb_gate", "arguments": {"text": "Hello"}}}
)
```

---

## Repository Root Resolution

When `repo_root` is not provided, AgentKB attempts to find the repository root by:
1. Walking upward from the current directory
2. Looking for `.agentkb/governance.yaml`

**Best Practice:** Run the MCP server from your repository root directory, or explicitly set `repo_root` in tool calls.

---

## Use Cases

### Pre-Disclosure Validation

Before an AI assistant sends output to a user, validate it:
```
Before responding, use agentkb_gate to check: "[your draft response]"
```

### Governance-Aware Assistants

Use the `agentkb_governance_context` prompt to make assistants aware of governance rules from the start.

### CI/CD Integration

Add AgentKB gate checks to your AI-powered documentation or code generation pipelines.

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| `governance.yaml not found` | Run from repo root or set `repo_root` parameter |
| MCP client can't connect | Verify Python path and MCP server is installed |
| HTTP transport 404 | Check bind address and port |
| Tool returns error | Check `agentkb doctor` for system health |

---

*For REST API integration, see [API_REFERENCE.md](API_REFERENCE.md).*
*For deployment options, see [DEPLOYMENT.md](DEPLOYMENT.md).*
