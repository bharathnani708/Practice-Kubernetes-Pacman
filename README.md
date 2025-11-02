# Kubernetes Pac-Man Project 🎮
Deploy a playable Pac-Man on Kubernetes while learning the essentials: Deployments, Services, Persistent Storage, Health Probes, Autoscaling (HPA), and High Availability.

## 📦 What’s inside
- `pacman.yaml` – all resources in one file (beginner friendly)
- `overlays/` (optional) – examples for splitting manifests (production style)

---

## 🧠 What you’ll learn
- **Namespace**: isolate the app in its own "folder"
- **Deployment (MongoDB)**: run the database with persistent storage
- **Service (MongoDB)**: give the DB a stable DNS name for other pods
- **Deployment (Pac-Man frontend)**: run multiple replicas for high availability
- **Service (Pac-Man)**: expose the web app to your browser
- **HPA**: autoscale based on CPU
- **PDB**: keep at least one pod running during maintenance

---

## ✅ Prerequisites
- `kubectl` installed
- **Minikube** (local K8s) → https://minikube.sigs.k8s.io/
- 2 vCPUs, 4GB RAM free recommended

> **Tip:** This guide uses **Minikube** for local/dev learning. For EKS/GKE/AKS notes, see **[Production & Future Improvements](#-production--future-improvements)**.

---

## 🚀 Quick Start

### 0) Clone the repo
```bash
git clone https://github.com/<you>/kubernetes-pac-man-project.git
cd kubernetes-pac-man-project
```

### 1) Start Minikube + enable metrics (for HPA)
```bash
minikube start --cpus=2 --memory=4g
minikube addons enable metrics-server
```


If metrics-server takes a bit to stabilize, that’s normal. HPA will show data after a minute or two.

2) Apply all manifests
kubectl apply -f pacman.yaml

3) Verify resources
kubectl -n pacman get all
kubectl -n pacman get pvc
kubectl -n pacman describe hpa pacman


You should see:

1 MongoDB pod, 2 Pac-Man pods

A mongo ClusterIP service and a pacman NodePort service

A bound PVC (mongo-storage)

An HPA targeting the pacman Deployment

4) Open Pac-Man in your browser

Option A (easiest):

minikube service pacman -n pacman --url


Open the printed URL.

Option B:

echo "http://$(minikube ip):30080"


Open that URL.

If you changed Service type to LoadBalancer (cloud), open the EXTERNAL-IP instead.

🧪 What to test
A) Self-healing

Delete the frontend pods and watch Kubernetes recreate them:

kubectl -n pacman delete pod -l app=pacman
kubectl -n pacman get pods -w

B) Autoscaling

Start a short load test and watch the HPA add pods:

# Run a temporary load pod
kubectl -n pacman run hey --rm -it --image=williamyeh/hey -- \
  -z 60s -c 10 http://pacman

# In another terminal
kubectl -n pacman get hpa -w
kubectl -n pacman get deploy pacman -w

C) Persistence (scores survive)

Recreate everything except the PVC:

kubectl -n pacman delete deploy,svc,hpa,pdb --all
kubectl apply -f pacman.yaml


Your high scores remain—MongoDB stored them on the PVC.
