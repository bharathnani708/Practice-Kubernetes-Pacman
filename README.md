# 🎮 Kubernetes Pac-Man — A Fun Way to Learn Kubernetes

Welcome to the **Kubernetes Pac-Man Project**!  
This is a beginner-friendly hands-on project where you’ll deploy a **Pac-Man web application** on a local **Minikube** cluster.

The goal is simple:  
✅ Learn the core Kubernetes components  
✅ Understand how they work together  
✅ See Kubernetes self-healing and scaling — while you play Pac-Man!

---

## 🧠 What You’ll Learn

| Concept | What It Means | Why It Matters |
|----------|----------------|----------------|
| **Namespace** | Logical isolation for resources | Keeps your app organized and easy to manage |
| **Deployment** | Blueprint that defines how pods are created | Ensures your app runs consistently and self-heals |
| **Service** | Network endpoint inside the cluster | Lets apps talk to each other or the outside world |
| **Persistent Volume Claim (PVC)** | Storage that survives pod restarts | Keeps Pac-Man high scores safe |
| **HPA (Horizontal Pod Autoscaler)** | Automatically adds/removes pods | Scales your app when load increases |
| **PDB (PodDisruptionBudget)** | Ensures minimum number of pods are always available | Provides high availability |

---

## ⚙️ Prerequisites

Make sure you have these installed:

- [Minikube](https://minikube.sigs.k8s.io/docs/start/)  
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)  
- 2 vCPUs and at least 4GB RAM free  

> 💡 This project is for **local learning environments** using Minikube.  
> For production (EKS/GKE/AKS), see the “Future Improvements” section below.

---

## 🚀 Step-by-Step Guide

### 1️⃣ Start Minikube
```bash
minikube start --cpus=2 --memory=4g
```

### 2️⃣ Enable Metrics Server (for Autoscaling)
```bash
minikube addons enable metrics-server
```
Wait a minute or two for the metrics server to stabilize

### 3️⃣ Deploy the Pac-Man App
Clone this repository and apply the manifest: (You can fork the repository and edit the URL in git clone command)
```bash
git clone https://github.com/bharathnani708/Practice-Kubernetes-Pacman.git
cd kubernetes-pac-man-project

kubectl apply -f pacman.yaml
```

### 4️⃣ Verify Everything Is Running
```bash
kubectl get all -n pacman
```
#### You should see:
mongo Deployment (database)
pacman Deployment (frontend, 2 replicas)
mongo Service (internal ClusterIP)
pacman Service (NodePort: 30080)
HPA and PDB resources

### 5️⃣ Open the Pac-Man Game 🎮
```bash
minikube service pacman -n pacman --url
```
Copy the URL and open it in your browser — start playing Pac-Man!

### 🔍 Testing Kubernetes Features
####🧩 A) Self-Healing
```bash
kubectl delete pod -l app=pacman -n pacman
kubectl get pods -n pacman -w
```
Kubernetes automatically recreates the deleted pod.
