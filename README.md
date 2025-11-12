# Atlan SRE Challenge – Kubernetes Troubleshooting & Observability

This repository contains the setup and fixes for the **Atlan SRE Challenge** — demonstrating hands-on debugging, Kubernetes networking, and observability practices.  
The environment intentionally starts with broken configurations and is then fixed step by step.

---

## 🧩 Scenario Overview

The setup simulates a small microservice architecture with:
- **Backend**: `hashicorp/http-echo` – responds with `hello-from-backend`
- **Frontend**: `curl` container hitting backend via Service
- **NetworkPolicy**: restrictive rules that initially block DNS & egress
- **Grafana + Prometheus**: for observability and analysis

---

## 🚀 Quick Start (Using Minikube)

```bash
# 1️⃣ Start Minikube
minikube start --cpus=4 --memory=6144 --kubernetes-version=stable

# 2️⃣ Enable metrics-server for HPA
minikube addons enable metrics-server

# 3️⃣ Deploy the initial broken setup
kubectl apply -f k8s/faulty/

# 4️⃣ Deploy the network debugging pod
kubectl apply -f k8s/tools/net-debug.yaml

# 5️⃣ Observe failures and investigate logs, DNS, and endpoints

# 6️⃣ Apply the fixes
kubectl apply -f k8s/fixed/

# 7️⃣ Set correct environment for frontend
kubectl set env deployment/frontend BACKEND_URL=http://backend-svc.default.svc.cluster.local:5678


ScreentShots










<img width="1934" height="1756" alt="image" src="https://github.com/user-attachments/assets/f751a6c4-22fa-42a8-bed9-ce2bba87797f" />


