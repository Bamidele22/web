---
title: "Kubernetes Basics: Container Orchestration at Scale"
description: "Master Kubernetes fundamentals and learn to orchestrate containerized applications across clusters"
pubDate: 2026-03-17
author: "Ayodeji Bamidele"
tags: ["kubernetes", "devops", "orchestration", "containers"]
---

Kubernetes (K8s) has become the de facto standard for container orchestration. As DevOps engineers, understanding Kubernetes is crucial for managing containerized workloads at enterprise scale.

## What is Kubernetes?

Kubernetes is an open-source container orchestration platform that automates deployment, scaling, and management of containerized applications. It handles the complexity of running containers across multiple machines.

### The Problem It Solves

Managing containers manually is tedious:
- Scheduling containers on nodes
- Handling container failures
- Managing networking between containers
- Scaling applications based on demand
- Rolling out updates without downtime

Kubernetes solves all of this automatically.

## Core Concepts

### Pods

The smallest deployable unit in Kubernetes. Usually contains one container (but can have multiple):

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
  - name: nginx
    image: nginx:latest
    ports:
    - containerPort: 80
```

### Deployments

Manages multiple pod replicas and ensures your desired state is maintained:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```

If a pod crashes, Kubernetes automatically restarts it. If you need more replicas, just change the number.

### Services

Services expose your pods to the network:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
```

### ConfigMaps

Store configuration data separately from your application:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  DATABASE_HOST: postgres.default.svc.cluster.local
  CACHE_TTL: "3600"
  DEBUG_MODE: "false"
```

### Secrets

Securely store sensitive data (passwords, API keys):

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: app-secrets
type: Opaque
data:
  database-password: cG9zdGdyZXMxMjM=  # base64 encoded
  api-key: YWJjZGVmZ2hpams=            # base64 encoded
```

## Getting Started

### Install Kubernetes Locally

```bash
# Using Minikube (easiest for learning)
brew install minikube  # macOS

# Start a cluster
minikube start

# Check status
kubectl cluster-info
kubectl get nodes
```

### Deploy Your First App

```bash
# Apply a manifest
kubectl apply -f deployment.yaml

# Check pods
kubectl get pods

# View logs
kubectl logs <pod-name>

# Access your app
kubectl port-forward svc/nginx-service 8080:80
```

## Why Kubernetes?

- **Auto-scaling** - Scale pods based on CPU/memory usage
- **Self-healing** - Automatically restart failed containers
- **Rolling updates** - Update applications with zero downtime
- **Load balancing** - Distribute traffic automatically
- **Multi-cloud** - Run on AWS, GCP, Azure, or on-premises
- **Infrastructure abstraction** - Write once, deploy anywhere

## Learning Roadmap

1. **Pods & Deployments** - How to run containers
2. **Services** - How containers talk to each other
3. **ConfigMaps & Secrets** - Configuration management
4. **StatefulSets** - For databases and stateful apps
5. **Ingress** - HTTP routing
6. **Persistent Volumes** - Data persistence
7. **RBAC** - Security and access control

## Common Gotchas

❌ **Don't use `latest` tags** - Use explicit versions
❌ **Don't run containers as root** - Set proper user permissions
❌ **Don't forget resource requests** - Helps scheduler assign workloads properly
✅ **Do use namespaces** - Organize resources logically
✅ **Do implement health checks** - Readiness and liveness probes
✅ **Do use network policies** - Restrict pod-to-pod communication

## Next Steps

1. Set up a local Kubernetes cluster with Minikube
2. Deploy a simple application
3. Learn about Helm (package manager for K8s)
4. Explore managed Kubernetes (EKS, GKE, AKS)
5. Master kubectl commands

Kubernetes has a steep learning curve, but it's worth every minute. Start small, experiment in non-production environments, and gradually master this powerful platform.

---

**Resources:**
- [Kubernetes Official Docs](https://kubernetes.io/docs/)
- [Kubernetes Training](https://kubernetes.io/training/)
