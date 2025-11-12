# Atlan SRE Challenge – Hands-on Kit

This repo gives you a quick, reproducible environment that *intentionally breaks* and then gets fixed.
It aligns with the Scenario, Objectives, Deliverables, and Evaluation Criteria in your PDF.

## Quick start (Kind + kube-prometheus-stack)

```bash
# 1) Create cluster + monitoring
./scripts/01_create_kind.sh

# 2) Deploy broken scenario
./scripts/02_deploy_faulty.sh

# 3) Run guided debugging (optional)
./scripts/03_debug_steps.sh

# 4) Apply fixes & verify
./scripts/04_deploy_fixed.sh
```

> Tip: To view Grafana locally:
```bash
kubectl -n monitoring port-forward svc/kps-grafana 3000:80
# login: admin / admin123
# import grafana/dashboard.json
```

## What’s broken (by design)
- **Service selector mismatch** for `backend-svc` (selector `app: backend` vs pod label `app: backend-api`).
- **Wrong DNS name** in `frontend` env (`BACKEND_URL=http://backend-svc-wrong:80`).
- **Restrictive NetworkPolicy** blocks all egress (including DNS).
- **Low memory limit** on frontend to flirt with OOM/restarts under load.

## What the fixes do
- Correct label selectors and DNS name.
- Relax egress to allow DNS (UDP/TCP 53) and common web ports; keep least privilege.
- Increase requests/limits for stability and add an HPA for burst tolerance.
- Scale backend and frontend to 2 replicas for resilience.

## Evidence to capture
- `kubectl get pods`, `kubectl describe pod`, `kubectl logs --previous` (CrashLoopBackOff/OOMKilled signals)
- `kubectl get endpoints backend-svc` (empty vs populated)
- DNS lookups from `net-debug` pod (`nslookup backend-svc`)
- Grafana panels: pod restarts, container memory, node MemoryPressure
- Successful curl/wget to `http://backend-svc` and via `frontend-svc` after fixes
