# Build and Deploy a Docker Image to a Kubernetes Cluster (GSP304)

![Google Cloud](https://img.shields.io/badge/Google_Cloud-4285F4?logo=googlecloud&logoColor=white)
![Lab](https://img.shields.io/badge/Lab-GSP304-34A853)
![Docker](https://img.shields.io/badge/Docker-2496ED?logo=docker&logoColor=white)
![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?logo=kubernetes&logoColor=white)

Reference solution for the Google Cloud challenge lab **Build and Deploy a Docker Image to a Kubernetes Cluster** (GSP304).

## 📖 Overview

You receive the source of a small web app called **echo-web**. You build it into a container image with Cloud Build, then deploy it on a new GKE cluster behind a public load balancer.

## 🧰 Services used

Cloud Build · Container Registry · Google Kubernetes Engine · Cloud Storage

## ✅ Prerequisites

```bash
export PROJECT_ID=$(gcloud config get-value project)
export ZONE="us-central1-a"
```

## 🚀 Solution

### 1. Download the application source

```bash
gsutil cp gs://$PROJECT_ID/echo-web.tar.gz .
# Older lab versions: gs://sureskills-ql/challenge-labs/ch04-kubernetes-app-deployment/echo-web.tar.gz
tar -xvf echo-web.tar.gz
```

### 2. Build and push the image with Cloud Build

```bash
gcloud builds submit --tag gcr.io/$PROJECT_ID/echo-app:v1 .
```

### 3. Create the GKE cluster

```bash
gcloud container clusters create echo-cluster \
  --zone $ZONE \
  --num-nodes 2 \
  --machine-type n1-standard-2

gcloud container clusters get-credentials echo-cluster --zone $ZONE
```

### 4. Deploy and expose the app

The app listens on port **8000**. The service exposes it on port **80**.

```bash
kubectl create deployment echo-web --image=gcr.io/$PROJECT_ID/echo-app:v1
kubectl expose deployment echo-web --type=LoadBalancer --port=80 --target-port=8000

kubectl get service echo-web --watch   # wait for EXTERNAL-IP
```

Open `http://<EXTERNAL-IP>` to verify the deployment.

---

## ⚠️ Notes

- Resource names, zones, regions and ports are generated per lab session. Always use the values shown in **your** lab instructions.
- These steps were written when the lab was live. Google revises labs over time, so the current version may differ.

## 📌 Disclaimer

This repository is a study reference. Attempt the lab on your own first, then compare approaches. It is not affiliated with or endorsed by Google.

## 👤 Author

**Girish Sharma** · Cloud & DevOps Engineer · [GitHub](https://github.com/codewithgarry) · [LinkedIn](https://www.linkedin.com/in/codewithgarry/)
