---
title: "Kubernetes Readiness Probes Prevent 502 Errors"
description: "Using readiness probes to handle graceful deployment transitions"
learnedDate: 2026-03-17
category: "Kubernetes"
tags: ["reliability", "orchestration"]
---

## The Discovery

Discovered that Kubernetes readiness probes automatically remove pods from load balancers during restarts, preventing users from hitting 502 errors.

## The Problem

When deploying new versions, traffic can still route to pods that are:
- Starting up and not ready to handle requests
- Shutting down during rolling updates
- Having issues with initialization

Result: 502 Bad Gateway errors for users during deploys.

## The Solution - Readiness Probe

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-server
spec:
  replicas: 3
  template:
    spec:
      containers:
      - name: api
        image: my-app:latest
        readinessProbe:
          httpGet:
            path: /health
            port: 3000
          initialDelaySeconds: 5
          periodSeconds: 10
```

## How It Works

1. **Pod starts** - Kubernetes waits for readiness probe to pass
2. **App initializes** - `/health` endpoint returns 200
3. **Pod marked ready** - Traffic is routed to it
4. **During shutdown** - Probe fails, traffic stops immediately
5. **Pod terminates** - No 502 errors

## Frontend Implementation

```javascript
// Simple health check endpoint
app.get('/health', (req, res) => {
  // Check dependencies: DB, cache, etc.
  if (allDependenciesHealthy()) {
    res.status(200).json({ status: 'healthy' });
  } else {
    res.status(503).json({ status: 'unhealthy' });
  }
});
```

## Result

- **Before**: 502 errors during deployments
- **After**: Seamless zero-downtime updates ✅

**Pro Tip**: Make your health check endpoint check all critical dependencies (database, caches, external services).
