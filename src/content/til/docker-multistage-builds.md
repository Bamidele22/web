---
title: "Docker Multi-Stage Builds Reduce Image Size by 90%"
description: "Building efficient Docker images using multi-stage approach"
learnedDate: 2026-03-18
category: "Docker"
tags: ["optimization", "containers"]
---

## The Discovery

Today I learned that multi-stage Docker builds can drastically reduce final image sizes. Instead of shipping everything in one layer, you can build in one stage and copy only artifacts to the final stage.

## The Problem

A typical Node.js Docker image with all build tools can be 800MB+. Most of this is just build dependencies that aren't needed in production.

## The Solution - Multi-Stage Build

```dockerfile
# Stage 1: Build stage
FROM node:18 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Stage 2: Production stage (final image)
FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY package*.json ./
RUN npm install --production
EXPOSE 3000
CMD ["node", "dist/index.js"]
```

## Results

- **Before**: 800MB (full Node.js with all dependencies)
- **After**: 80MB (Alpine base with only production deps)
- **Savings**: 90% reduction! ✨

## Why It Matters

- Faster deployments
- Less storage cost
- Quicker container startup
- Smaller attack surface

**Lesson**: Always think about what's actually needed in your final image, not what was needed to build it.
