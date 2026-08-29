# Command Sequence — GKE Autopilot Build

The exact PowerShell sequence run on Windows, August 2026, with captured output. Project: `gke-portfolio-2026`, region: `us-central1`.

## 1. Create the Autopilot cluster

```powershell
gcloud container clusters create-auto gke-portfolio-auto --region=us-central1
```

```
Creating cluster gke-portfolio-auto in us-central1... Cluster is being
health-checked (Kubernetes Control Plane is healthy)...done.
Created [.../clusters/gke-portfolio-auto].
NAME                LOCATION      MASTER_VERSION      MACHINE_TYPE   NUM_NODES  STATUS
gke-portfolio-auto  us-central1   1.35.7-gke.1027000  ek-standard-8  3          RUNNING
```

## 2. Wire up kubectl and confirm

```powershell
gcloud container clusters get-credentials gke-portfolio-auto --region=us-central1
kubectl get nodes
```

```
Fetching cluster endpoint and auth data.
kubeconfig entry generated for gke-portfolio-auto.

NAME                                                STATUS   ROLES    AGE   VERSION
gk3-gke-portfolio-auto-default-pool-be28be3f-hl2z   Ready    <none>   87s   v1.35.7-gke.1027000
```

## 3. Deploy NGINX

```powershell
kubectl create deployment web --image=nginx
kubectl get pods
```

```
deployment.apps/web created

NAME                   READY   STATUS    RESTARTS   AGE
web-5588659d85-p2kkv   0/1     Pending   0          12s
# ...re-run...
web-5588659d85-p2kkv   1/1     Running   0          63s
```

## 4. Expose it to the internet

```powershell
kubectl expose deployment web --port=80 --type=LoadBalancer
kubectl get service web
```

```
service/web exposed

NAME   TYPE           CLUSTER-IP       EXTERNAL-IP     PORT(S)        AGE
web    LoadBalancer   34.118.234.192   <pending>       80:32189/TCP   19s
# ...re-run until the external IP resolves...
web    LoadBalancer   34.118.234.192   136.115.147.7   80:32189/TCP   56s
```

Opened `http://136.115.147.7` in a browser — NGINX welcome page confirmed the workload was live and publicly served.

## 5. Scale and capture evidence

```powershell
kubectl scale deployment web --replicas=3
kubectl get deployment,service,pods -o wide
kubectl get deployment,service,pods -o wide > gke-state.txt
```

```
deployment.apps/web scaled

NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/web   3/3     3            3           4m33s

service/web   LoadBalancer   34.118.234.192   136.115.147.7   80:32189/TCP   3m17s   app=web

pod/web-5588659d85-82fnn   1/1   Running   0   10s   ...pool-1-2a4958ef-7c2p
pod/web-5588659d85-djgtx   1/1   Running   0   11s   ...pool-1-b41e186e-48xf
pod/web-5588659d85-p2kkv   1/1   Running   0   4m34s ...pool-1-d9a6c641-8sgs
```

## 6. Tear down — same session (service first, then cluster)

```powershell
kubectl delete service web
gcloud container clusters delete gke-portfolio-auto --region=us-central1 --quiet
```

```
service "web" deleted from default namespace
Deleting cluster gke-portfolio-auto...done.
Deleted [.../clusters/gke-portfolio-auto].
```

## 7. Verify zero — no orphaned billing resources

```powershell
gcloud container clusters list
gcloud compute forwarding-rules list
```

```
# clusters list: (empty)
# forwarding-rules list: Listed 0 items.
```

---
© 2026 David Grunwald. All rights reserved.
