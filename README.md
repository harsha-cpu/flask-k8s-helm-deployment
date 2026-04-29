# Flask Kubernetes Helm Deployment

## Project Overview

This project demonstrates a complete DevOps workflow by building a Python Flask web application, containerizing it with Docker, deploying it to a local Kubernetes cluster using kind, managing releases with Helm, and publishing the source code to GitHub. It was created as a hands-on learning project to practice real deployment operations.

---

# Technologies Used

* Ubuntu Virtual Machine
* VirtualBox
* Python Flask
* Docker
* Kubernetes (kind)
* kubectl
* Helm
* Git
* GitHub

---

# Project Structure

```text
simpleapp/
├── app.py
├── Dockerfile
└── myapp-chart/
```

---

# Step-by-Step Implementation

---

## Step 1: Create Ubuntu Virtual Machine

Installed Ubuntu in VirtualBox and configured NAT networking.

### Purpose

Used Ubuntu as an isolated Linux environment for DevOps practice.

---

## Step 2: Install Required Tools

Installed all required tools:

```bash
sudo apt update
sudo apt install git docker.io curl -y
```

Installed:

* Git
* Docker
* kind
* kubectl
* Helm

### Purpose

Required for containerization and Kubernetes management.

---

## Step 3: Create Flask Application

Created `app.py`

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello from Kubernetes + Helm!"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

### Purpose

Simple web application for deployment testing.

---

## Step 4: Create Dockerfile

Created Dockerfile:

```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY app.py .

RUN pip install flask

EXPOSE 5000

CMD ["python", "app.py"]
```

### Purpose

Convert application into portable container image.

---

## Step 5: Build Docker Image

```bash
docker build -t myapp:v1 .
```

### Purpose

Create version 1 image of application.

---

## Step 6: Run Docker Container

```bash
docker run -d -p 5001:5000 --name myapp-container myapp:v1
```

### Access

```text
http://127.0.0.1:5001
```

### Purpose

Run container locally and verify app works.

---

## Step 7: Create Kubernetes Cluster

```bash
kind create cluster --name devlab
```

### Purpose

Create local Kubernetes environment.

---

## Step 8: Load Docker Image into kind

```bash
kind load docker-image myapp:v1 --name devlab
```

### Purpose

kind cluster needs local image manually loaded.

---

## Step 9: Deploy Application to Kubernetes

```bash
kubectl create deployment myapp --image=myapp:v1
```

### Verify

```bash
kubectl get pods
```

---

## Step 10: Expose Application

```bash
kubectl expose deployment myapp --type=NodePort --port=5000 --target-port=5000
```

### Verify

```bash
kubectl get svc
```

---

## Step 11: Scale Application

```bash
kubectl scale deployment myapp --replicas=3
```

### Purpose

Increase pod count for availability and load balancing.

---

## Step 12: Upgrade Application to v2

Updated `app.py`

```python
return "Hello from Kubernetes + Helm v2!"
```

Built new image:

```bash
docker build --no-cache -t myapp:v2 .
kind load docker-image myapp:v2 --name devlab
```

Performed rolling update:

```bash
kubectl set image deployment/myapp myapp=myapp:v2
kubectl rollout status deployment/myapp
```

### Purpose

Deploy new version without downtime.

---

## Step 13: Create Helm Chart

```bash
helm create myapp-chart
```

### Purpose

Package Kubernetes manifests into reusable chart.

---

## Step 14: Configure values.yaml

Updated:

```yaml
replicaCount: 3

image:
  repository: myapp
  tag: "v2"

service:
  type: NodePort
  port: 5000
```

---

## Step 15: Install Helm Release

```bash
cd myapp-chart
helm install myrelease .
```

### Verify

```bash
helm list
kubectl get all
```

---

## Step 16: Upgrade Helm Release

```bash
helm upgrade myrelease .
```

### Purpose

Apply configuration or version changes.

---

## Step 17: Rollback Helm Release

```bash
helm rollback myrelease 2
```

### Purpose

Restore previous stable release.

---

## Step 18: ConfigMap

```bash
kubectl create configmap app-config --from-literal=ENV=prod --from-literal=LOG_LEVEL=info
```

### Purpose

Store non-sensitive configuration.

---

## Step 19: Secret

```bash
kubectl create secret generic db-secret --from-literal=username=admin --from-literal=password=Pass123
```

### Purpose

Store sensitive values securely.

---

## Step 20: Publish to GitHub

```bash
git init
git add .
git commit -m "DevOps project ready"
git branch -M main
git push -u origin main --force
```

---

# Final Result

* Flask app deployed on Kubernetes
* Managed by Helm
* 3 running replicas
* Rolling updates completed
* Rollback tested
* Code published to GitHub

---

# Skills Learned

* Docker
* Kubernetes
* Helm
* Scaling
* Rolling Updates
* Rollback
* ConfigMap
* Secret
* GitHub Version Control

---

# Future Improvements

* GitHub Actions CI/CD
* Jenkins
* Terraform
* AWS EKS
* Monitoring with Prometheus & Grafana

---

# Author

Harsha
