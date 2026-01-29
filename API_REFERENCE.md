# AgentKB REST API Reference

Base URL: `http://localhost:8000`

---

## Quick Start

```bash
# Install with server dependencies
pip install agentkb[server]

# Start the server
uvicorn agentkb.server.api:app --host 0.0.0.0 --port 8000

# Test
curl http://localhost:8000/v1/doctor
```

---

## Endpoints

### `GET /v1/doctor`

Preflight diagnostics — verify governance loads and system is ready.

**Query Parameters:**
| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `repo_root` | string | auto-detect | Path to AgentKB repository root |

**Response:**
```json
{
  "schema": "doctor_result.v1",
  "ok": true,
  "checks": [
    {"name": "governance_load", "status": "ok"},
    {"name": "derived_dir_writable", "status": "ok"}
  ]
}
```

**Example:**
```bash
curl http://localhost:8000/v1/doctor
```

---

### `POST /v1/gate`

Run the Output Gate on a draft text. Detects PII, secrets, and unverified claims.

**Request Body:**
```json
{
  "text": "The API key is sk-abc123 and we grew 50%",
  "output_tier": "external",
  "role": "reader",
  "log": true
}
```

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `text` | string | ✓ | — | Text to validate |
| `output_tier` | string | | `external` | `external`, `internal`, or `strategic` |
| `role` | string | | `reader` | RBAC role for validation |
| `actor` | string | | `agent` | Attribution for audit logging |
| `secret_tokens` | array | | `[]` | Additional tokens to treat as SECRET |
| `repo_root` | string | | auto-detect | Repository root path |
| `log` | boolean | | `true` | Log error events on blocks |

**Response:**
```json
{
  "schema": "gate_result.v1",
  "role": "reader",
  "decision": "block",
  "violations": [
    {
      "invariant_id": "sensitivity.levels.SECRET.output",
      "error_type": "secret_exposure",
      "severity": "critical",
      "message": "SECRET token detected"
    },
    {
      "invariant_id": "claims.enforcement.unsourced_facts_forbidden",
      "error_type": "metric_fabrication",
      "severity": "medium",
      "message": "Quantified claim without evidence"
    }
  ],
  "output": "The API key is <REDACTED:SECRET> and we grew <REDACTED:CLAIM>",
  "governance": {
    "codex_version": "0.3.9",
    "sha256": "abc123..."
  }
}
```

**Example:**
```bash
curl -X POST http://localhost:8000/v1/gate \
  -H "Content-Type: application/json" \
  -d '{"text":"Hello world","output_tier":"external"}'
```

---

### `POST /v1/chat`

Chat with an LLM, with Output Gate enforcement on every response.

**Request Body:**
```json
{
  "message": "Summarize our Q4 results",
  "session_id": null,
  "llm_provider": "anthropic",
  "llm_model": "claude-3-sonnet-20240229",
  "output_tier": "external",
  "role": "reader",
  "max_retries": 1
}
```

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `message` | string | ✓ | — | User message |
| `session_id` | string | | `null` | Existing session ID (creates new if null) |
| `llm_provider` | string | | `ollama` | `ollama`, `anthropic`, `openai`, `xai`, `mock` |
| `llm_model` | string | | provider default | Model name |
| `output_tier` | string | | `external` | Output tier for gate |
| `role` | string | | `reader` | RBAC role |
| `max_retries` | int | | `1` | Repair attempts on gate block |
| `log` | boolean | | `true` | Log events |

**Provider Configuration:**
| Provider | Environment Variable | Example Model |
|----------|---------------------|---------------|
| `ollama` | — (local) | `llama3.1:8b` |
| `anthropic` | `ANTHROPIC_API_KEY` | `claude-3-sonnet-20240229` |
| `openai` | `OPENAI_API_KEY` | `gpt-4` |
| `xai` | `XAI_API_KEY` | `grok-beta` |
| `mock` | — | — (uses `mock_output` param) |

**Response:**
```json
{
  "schema": "chat_result.v1",
  "session_id": "uuid-here",
  "role": "reader",
  "message": "Based on the available data...",
  "gate": {
    "decision": "allow",
    "violations": [],
    "attempts": 1
  },
  "governance": {
    "codex_version": "0.3.9",
    "sha256": "abc123..."
  }
}
```

**Session Management:**
- Sessions are **in-memory** with 1-hour TTL
- Activity extends session expiration
- Expired sessions return `404 Not Found`
- Session governance SHA must match current governance (returns `409` on mismatch)

**Example:**
```bash
curl -X POST http://localhost:8000/v1/chat \
  -H "Content-Type: application/json" \
  -d '{"message":"Hello","llm_provider":"mock","mock_output":"Hello!"}'
```

---

### `GET /v1/context`

Verify context integrity invariants and get session fingerprint.

**Query Parameters:**
| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `repo_root` | string | auto-detect | Repository root path |
| `role` | string | `reader` | RBAC role to verify |

**Response:**
```json
{
  "schema": "context_integrity_result.v2",
  "verified": true,
  "failed_count": 0,
  "invariants": [
    {"id": "governance_loaded", "check": "Governance loaded", "required": true, "passed": true},
    {"id": "time_anchored", "check": "Time anchored", "required": true, "passed": true},
    {"id": "role_confirmed", "check": "Role confirmed", "required": true, "passed": true}
  ],
  "time_anchor": {
    "utc": "2026-01-29T18:00:00Z",
    "local": "2026-01-29T12:00:00-06:00",
    "timezone": "America/Chicago",
    "offset": "-06:00"
  },
  "fingerprint": {
    "governance_sha": "abc123...",
    "governance_version": "0.3.9",
    "role_id": "reader",
    "time_anchor_utc": "2026-01-29T18:00:00Z",
    "fingerprint_hash": "def456..."
  },
  "governance": {
    "codex_version": "0.3.9",
    "sha256": "abc123..."
  }
}
```

---

### `GET /v1/gcs`

Query current Governance Compliance Score (GCS) and gate eligibility.

**Query Parameters:**
| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `repo_root` | string | auto-detect | Repository root path |
| `window_hours` | int | `24` | Time window for violations |

**Response:**
```json
{
  "schema": "gcs_result.v1",
  "score": 100,
  "violations_in_window": 0,
  "penalties_applied": 0,
  "passive_recovery": 0,
  "active_recovery": 0,
  "gates": {
    "rule_proposal": {"eligible": true, "threshold": 80},
    "content_mutation": {"eligible": true, "threshold": 85},
    "architectural_decision": {"eligible": true, "threshold": 90},
    "capability_removal": {"eligible": true, "threshold": 95}
  },
  "governance": {
    "codex_version": "0.3.9",
    "sha256": "abc123..."
  }
}
```

---

### `POST /v1/gcs/repair`

Record a repair action for a violation to recover GCS points.

**Request Body:**
```json
{
  "error_id": "uuid-of-error",
  "repair_type": "explain",
  "actor": "agent"
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `error_id` | string | ✓ | UUID of the error event |
| `repair_type` | string | ✓ | `acknowledge` (+10), `explain` (+15), or `propose` (+20) |
| `actor` | string | | Attribution (default: `agent`) |

**Response:**
```json
{
  "schema": "gcs_repair_result.v1",
  "success": true,
  "error_id": "uuid-of-error",
  "repair_type": "explain",
  "points_awarded": 15,
  "gcs_before": 85,
  "gcs_after": 100,
  "governance": {
    "codex_version": "0.3.9",
    "sha256": "abc123..."
  }
}
```

---

### `GET /v1/audit`

Query audit log for events.

**Query Parameters:**
| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `action` | string | — | Filter by action type |
| `decision` | string | — | Filter by `allow` or `block` |
| `role` | string | — | Filter by role |
| `limit` | int | `100` | Maximum results |
| `offset` | int | `0` | Pagination offset |

**Response:**
```json
{
  "schema": "audit_query_result.v1",
  "events": [
    {
      "id": "uuid",
      "ts_utc": "2026-01-29T18:00:00Z",
      "actor": "agent",
      "action": "gate",
      "decision": "allow",
      "role": "reader"
    }
  ],
  "total": 1,
  "limit": 100,
  "offset": 0
}
```

---

### `GET /v1/audit/metrics`

Get aggregate audit metrics.

**Query Parameters:**
| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `window_hours` | int | `24` | Time window |

**Response:**
```json
{
  "schema": "audit_metrics_result.v1",
  "window_hours": 24,
  "total_events": 150,
  "by_decision": {
    "allow": 140,
    "block": 10
  },
  "by_action": {
    "gate": 100,
    "chat": 50
  },
  "block_rate": 0.067
}
```

---

## Error Responses

| Status | Meaning |
|--------|---------|
| `400` | Bad request (missing required field, invalid JSON) |
| `403` | Unknown role (RBAC fail-closed) |
| `404` | Session not found or expired |
| `409` | Governance SHA changed mid-session |
| `502` | LLM provider error |

**Error Response Format:**
```json
{
  "detail": "Error description"
}
```

---

## OpenAPI / Swagger

When the server is running, interactive API documentation is available at:
- **Swagger UI:** `http://localhost:8000/docs`
- **ReDoc:** `http://localhost:8000/redoc`

---

*For deployment instructions, see [DEPLOYMENT.md](DEPLOYMENT.md).*
