# Blue-Green Deployment Project

## Overview

This project demonstrates the deployment of a containerized Node.js application using Docker and Kubernetes with a Blue-Green Deployment strategy.

The application consists of:

* Backend Node.js/Express application
* MongoDB database
* Blue Frontend version
* Green Frontend version
* Kubernetes Services and Deployments
* Blue-Green traffic switching using Kubernetes Service selectors

---

# Prerequisites

* Docker
* Kubernetes (Minikube)
* kubectl
* Node.js
* Git

---

# Project Structure

```text
.
├── backend
├── frontend-blue
├── frontend-green
├── k8s
│   ├── backend-deployment.yaml
│   ├── frontend-blue-deployment.yaml
│   ├── frontend-green-deployment.yaml
│   ├── frontend-service.yaml
│   └── ingress.yaml
└── README.md
```

---

# Local Deployment

## Backend Setup

Navigate to backend directory:

```bash
cd backend
npm install
```

Create `.env` file:

```env
PORT=5000
MONGO_URI=mongodb://localhost:27017/mydb
```

Start backend:

```bash
npm start
```

Verify:

```bash
curl http://localhost:5000/health
```
<img width="600" height="210" alt="Screenshot 2026-06-07 181448" src="https://github.com/user-attachments/assets/b35a7735-5f44-41c7-ac9a-60d67a3176d8" />

---

## Frontend Blue Setup

```bash
cd frontend-blue
npm install
npm start
```

Access:

```text
http://localhost:3100
```

<img width="1777" height="877" alt="3100" src="https://github.com/user-attachments/assets/4c75934b-71cf-483b-b302-70533153338b" />

---

## Frontend Green Setup

```bash
cd frontend-green
npm install
npm start
```

Access:

```text
http://localhost:3200
```
<img width="1752" height="952" alt="image" src="https://github.com/user-attachments/assets/8fac0283-b69f-4e7d-a5ea-822ae3b04e6a" />

---

# Dockerization

## Build Backend Image

```bash
docker build -t madhavasai2907/backend:v1 ./backend
```

## Build Blue Frontend Image

```bash
docker build -t madhavasai2907/frontend-blue:v1 ./frontend-blue
```

## Build Green Frontend Image

```bash
docker build -t madhavasai2907/frontend-green:v1 ./frontend-green
```

---

## Push Images to Docker Hub

```bash
docker push madhavasai2907/backend:v1

docker push madhavasai2907/frontend-blue:v1

docker push madhavasai2907/frontend-green:v1
```
<img width="820" height="342" alt="Screenshot 2026-06-07 180044" src="https://github.com/user-attachments/assets/1be17083-ca2f-4b7c-b167-27d2d1dce601" />

<img width="1498" height="178" alt="image" src="https://github.com/user-attachments/assets/6c183cfc-0e72-4de5-9155-5eefa607dd97" />

---

# Kubernetes Deployment

## Start Minikube

```bash
minikube start
```

Enable required addons:

```bash
minikube addons enable ingress
minikube addons enable metrics-server
```

---

## Deploy Application

Apply all manifests:

```bash
kubectl apply -f k8s/
```

Verify deployments:

```bash
kubectl get deployments
```

Verify pods:

```bash
kubectl get pods
```

Verify services:

```bash
kubectl get svc
```
<img width="820" height="342" alt="Screenshot 2026-06-07 180044" src="https://github.com/user-attachments/assets/e74d3183-4c6a-423c-aea9-3f27ee6e5bac" />

---

# Blue-Green Deployment Strategy

The application uses two frontend deployments:

## Blue Deployment

```text
app=frontend
version=blue
```
<img width="842" height="326" alt="Screenshot 2026-06-07 180107" src="https://github.com/user-attachments/assets/d0a483f7-d9c2-45a3-a565-af1d627a17b5" />


## Green Deployment

```text
app=frontend
version=green
```
<img width="832" height="367" alt="Screenshot 2026-06-07 180122" src="https://github.com/user-attachments/assets/6781db8b-af2d-4f8c-a66b-c4f6d1dcf3af" />

The Kubernetes Service routes traffic based on labels.

Initial Service configuration:

```yaml
selector:
  app: frontend
  version: blue
```

Traffic flow:

```text
Users
  |
Frontend Service
  |
Blue Pods
```

---

# Switching Traffic to Green

Run:

```bash
kubectl patch service frontend-service \
-p '{"spec":{"selector":{"app":"frontend","version":"green"}}}'
```

Verify:

```bash
kubectl describe svc frontend-service
```

Traffic flow:

```text
Users
  |
Frontend Service
  |
Green Pods
```

---

# Switching Traffic Back to Blue

```bash
kubectl patch service frontend-service \
-p '{"spec":{"selector":{"app":"frontend","version":"blue"}}}'
```

# Cleanup

Delete all resources:

kubectl delete -f k8s/

Stop Minikube:

minikube stop


```
```
