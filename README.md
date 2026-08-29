# GKE Autopilot Portfolio

**Stood up a Google Kubernetes Engine (GKE) Autopilot cluster from an empty account (Aug 2026): configured project and billing, deployed and scaled an NGINX workload, exposed it to the internet via a cloud load balancer, and tore the environment down cleanly — full command sequence and artifacts in one repo.**

## What I Accomplished

Provisioned a managed Kubernetes cluster on GKE Autopilot on Windows using the `gcloud` CLI, from project and billing setup through a live, internet-exposed Deployment and safe teardown. The NGINX workload was scaled to three replicas across multiple Autopilot-provisioned nodes and served publicly through a Google Cloud load balancer, then fully decommissioned in the same session with a verified return to zero running resources. Demonstrates comfort with the declarative Kubernetes model — Deployments, Services, and horizontal scaling — and with cost-aware cloud hygiene.

## What / When / How

- **What:** A reproducible, end-to-end GKE Autopilot build — cluster creation, workload deployment, public exposure via LoadBalancer, horizontal scaling, and clean teardown.
- **When:** August 2026.
- **How:** Windows + PowerShell, Google Cloud CLI (`gcloud`), and `kubectl` against GKE Autopilot in `us-central1`. No manual node management — Autopilot provisioned and scaled nodes on demand.

This is recent, reproducible portfolio evidence — a first hands-on GKE build, clearly dated — not a claim of production Kubernetes operations.

## Evidence

| Artifact | File | Shows |
|---|---|---|
| Cluster state | `gke-state.txt` | 3/3 pods Running, LoadBalancer with external IP, across two nodes |
| Live service | `nginx-live.png` | NGINX welcome page served at `http://136.115.147.7` |
| LoadBalancer | `loadbalancer-service.png` | `kubectl get service web` showing the assigned `EXTERNAL-IP` |
| Full command log | `commands.md` | Exact PowerShell sequence and captured output, create → teardown |

### Verified end state

The cluster and load balancer were deleted in the same session. `gcloud container clusters list` returned empty and `gcloud compute forwarding-rules list` returned zero items — no orphaned billing resources.

## Repository Layout

```
gke-autopilot-portfolio/
  README.md                    # this file
  commands.md                  # exact command sequence + output
  gke-state.txt                # kubectl get deployment,service,pods -o wide
  nginx-live.png               # browser screenshot of the served page
  loadbalancer-service.png     # kubectl get service web (external IP)
```

## Where This Leads

Next rungs that build directly on this cluster: declarative manifests + GitOps (`kubectl apply`), an Ingress with managed TLS, a CI/CD pipeline pushing to Artifact Registry, a Standard-mode cluster with node pools, and — as a separate advanced guide — GPU/TPU accelerator workloads (vLLM inference, a LoRA fine-tuning Job, and a TPU v5e smoke test).

---
© 2026 David Grunwald. All rights reserved.
