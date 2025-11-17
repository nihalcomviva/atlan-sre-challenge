# SRE Challenge – Short Report

## 1) Root Causes
- **RC1 (Service discovery):** Backend Service selector mismatch -> no Endpoints -> frontend couldn't resolve/reach backend.
- **RC2 (Stability):** Frontend CrashLoopBackOff due to wrong BACKEND_URL and tight memory limits; occasional OOMKilled.
- **RC3 (Networking):** Default-deny egress blocked DNS (UDP/TCP 53) and external traffic.

## 2) Fixes
- **Fix1:** Align `app` labels and Service selectors; correct `BACKEND_URL` to `http://backend-svc.default.svc.cluster.local:80`.
- **Fix2:** Requests/limits tuned (128Mi/256Mi) + HPA (2–5) for autoscaling. Increased backend replicas to 2 for resilience.
- **Fix3:** Replace default-deny with explicit `allow-dns-and-web-egress` NetworkPolicy.

## 3) Monitoring Findings
- Frontend restart spikes correlated with memory limit and failed backend calls.
- Node MemoryPressure toggled during bursts.
- After fixes: restarts flat, memory stabilized below limits.

## 4) Verification
- `kubectl get endpoints backend-svc` now lists pod IPs.
- `nslookup backend-svc` works from `net-debug`.
- `wget -qO- http://backend-svc` returns expected payload.
- Grafana shows normal metrics; no new restarts.

## 5) Preventive Improvements
- CI policy to fail on Service selector mismatches and invalid DNS/env (policy-as-code).
- Alerts: pod restarts > N/15m, node MemoryPressure, empty endpoints for Services.
- Runbook for DNS/NetworkPolicy triage; regular load tests and chaos drills.
