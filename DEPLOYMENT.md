# AgentKB Deployment Guide

This guide covers deployment of the AgentKB **REST API server** for production environments.

## Prerequisites

- Python 3.12+
- Docker (for containerized deployment)
- Kubernetes (for orchestrated deployment)
- LLM provider API keys (optional, for `/v1/chat` endpoint)

---

## Docker

### Build

```bash
docker build -t agentkb:latest .
```

### Run

The API server requires access to:
- `.agentkb/governance.yaml` — Governance rules (read-only)
- `.agentkb/derived/` — Runtime artifacts (writable, for audit/error logs)

```bash
docker run -p 8000:8000 \
  -v /path/to/your/.agentkb:/app/.agentkb \
  -e ANTHROPIC_API_KEY=$ANTHROPIC_API_KEY \
  -e OPENAI_API_KEY=$OPENAI_API_KEY \
  agentkb:latest
```

**Verify:**

```bash
curl http://localhost:8000/v1/doctor
```

### Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `ANTHROPIC_API_KEY` | For Anthropic LLM | Claude API key |
| `OPENAI_API_KEY` | For OpenAI LLM | GPT API key |
| `XAI_API_KEY` | For xAI LLM | Grok API key |

**Note:** API keys are only required if using the `/v1/chat` endpoint with cloud LLM providers. The `/v1/gate` endpoint works without any API keys.

### Docker Compose (Example)

```yaml
version: '3.8'
services:
  agentkb:
    build: .
    ports:
      - "8000:8000"
    volumes:
      - ./governance:/app/.agentkb:ro
      - agentkb-derived:/app/.agentkb/derived
    environment:
      - ANTHROPIC_API_KEY=${ANTHROPIC_API_KEY}
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8000/v1/doctor"]
      interval: 30s
      timeout: 10s
      retries: 3

volumes:
  agentkb-derived:
```

---

## Kubernetes

### Overview

AgentKB Kubernetes deployment consists of:
- **Deployment** — API server pods
- **Service** — Load balancer or ClusterIP
- **ConfigMap** — Governance rules
- **Secret** — LLM provider API keys

### Create Secret

```yaml
# agentkb-secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: agentkb-secrets
type: Opaque
stringData:
  anthropic-api-key: "your-anthropic-key"
  openai-api-key: "your-openai-key"
```

```bash
kubectl apply -f agentkb-secret.yaml
```

### Create ConfigMap (Governance)

```yaml
# agentkb-governance.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: agentkb-governance
data:
  governance.yaml: |
    # Your governance.yaml content here
    schema_version: "0.3"
    # ... (see agentkb init for defaults)
```

```bash
kubectl apply -f agentkb-governance.yaml
```

### Deployment

```yaml
# agentkb-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: agentkb
spec:
  replicas: 1  # See scaling note below
  selector:
    matchLabels:
      app: agentkb
  template:
    metadata:
      labels:
        app: agentkb
    spec:
      containers:
      - name: agentkb
        image: agentkb:latest
        ports:
        - containerPort: 8000
        env:
        - name: ANTHROPIC_API_KEY
          valueFrom:
            secretKeyRef:
              name: agentkb-secrets
              key: anthropic-api-key
        volumeMounts:
        - name: governance
          mountPath: /app/.agentkb/governance.yaml
          subPath: governance.yaml
          readOnly: true
        livenessProbe:
          httpGet:
            path: /v1/doctor
            port: 8000
          initialDelaySeconds: 10
          periodSeconds: 30
      volumes:
      - name: governance
        configMap:
          name: agentkb-governance
```

### Service

```yaml
# agentkb-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: agentkb
spec:
  selector:
    app: agentkb
  ports:
  - port: 80
    targetPort: 8000
  type: ClusterIP  # Or LoadBalancer for external access
```

### Apply All

```bash
kubectl apply -f agentkb-secret.yaml
kubectl apply -f agentkb-governance.yaml
kubectl apply -f agentkb-deployment.yaml
kubectl apply -f agentkb-service.yaml
```

---

## Scaling Considerations

### Stateless Endpoints

The following endpoints are **stateless** and can scale horizontally:
- `GET /v1/doctor`
- `POST /v1/gate`
- `GET /v1/context`
- `GET /v1/gcs`

### Stateful Endpoints

The `/v1/chat` endpoint maintains **in-memory sessions**:
- **Session TTL:** 2 hours (activity extends expiration)
- **Memory safety:** Expired sessions are lazily evicted

For multiple replicas with `/v1/chat`:
- Implement sticky sessions (session affinity)
- Or use an external session store (Redis)
- Or route chat traffic to a dedicated stateful pod

**Recommendation:** Keep replicas at 1 unless you only need stateless endpoints.

---

## Health Checks

| Endpoint | Purpose |
|----------|---------|
| `GET /v1/doctor` | Full health check (governance, derived dir) |
| `GET /health` | Simple liveness probe |

### Kubernetes Probes

```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 8000
  initialDelaySeconds: 5
  periodSeconds: 10

readinessProbe:
  httpGet:
    path: /v1/doctor
    port: 8000
  initialDelaySeconds: 10
  periodSeconds: 30
```

---

## Security Considerations

1. **API Keys** — Never commit secrets to version control. Use Kubernetes Secrets or a secrets manager.

2. **Network Policy** — Restrict access to AgentKB pods. Only allow traffic from trusted services.

3. **TLS** — Deploy behind an ingress controller with TLS termination (nginx-ingress, traefik, etc.).

4. **RBAC** — Use Kubernetes RBAC to limit who can access AgentKB resources.

5. **Audit Logs** — Mount `.agentkb/derived/` to persistent storage for compliance audit retention.

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| `governance.yaml not found` | Verify ConfigMap mount path |
| `derived directory not writable` | Check volume permissions |
| `LLM provider error` | Verify API key environment variables |
| Pod crash loop | Check `kubectl logs` for startup errors |

---

*For API endpoint details, see [API_REFERENCE.md](API_REFERENCE.md).*
