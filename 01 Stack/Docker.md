---
title: Docker & Containerization
date: 2026-03-30
tags: [docker, containerization, devops, deployment]
category: DevOps-Tools
author: OpenCode
version: 1.0.0
status: active
---

# Docker & Containerization - Complete Guide

Docker enables consistent application deployment across development, testing, and production environments through containerization. This guide covers Docker basics, Docker Compose, and best practices.

## Table of Contents
1. [Installation & Setup](#installation--setup)
2. [Docker Fundamentals](#docker-fundamentals)
3. [Dockerfile & Images](#dockerfile--images)
4. [Containers & Running Apps](#containers--running-apps)
5. [Docker Compose](#docker-compose)
6. [Networking](#networking)
7. [Volumes & Persistence](#volumes--persistence)
8. [Image Optimization](#image-optimization)
9. [Multi-Stage Builds](#multi-stage-builds)
10. [Best Practices](#best-practices)

---

## Installation & Setup

### macOS Installation

```bash
# Install via Homebrew
brew install docker

# Or install Docker Desktop (includes Docker Compose, CLI tools)
brew install --cask docker

# Verify installation
docker --version
docker run hello-world
```

### Windows Installation

**Option 1: Docker Desktop**
- Download from [Docker Desktop](https://www.docker.com/products/docker-desktop)
- Install and enable WSL2 backend

**Option 2: PowerShell with Chocolatey**
```powershell
choco install docker-desktop

# Or using Windows Package Manager
winget install Docker.DockerDesktop

# Verify installation
docker --version
```

### Linux Installation (Ubuntu)

```bash
# Install Docker
sudo apt update
sudo apt install docker.io

# Enable service
sudo systemctl start docker
sudo systemctl enable docker

# Run without sudo (optional)
sudo usermod -aG docker $USER
newgrp docker
```

---

## Docker Fundamentals

### Key Concepts

| Concept | Description |
|---------|-------------|
| **Image** | Lightweight, standalone executable package |
| **Container** | Running instance of an image |
| **Registry** | Repository for storing images (Docker Hub, etc.) |
| **Dockerfile** | Instructions to build an image |
| **Layer** | Read-only component of an image |

### Basic Commands

```bash
# List local images
docker images

# List running containers
docker ps

# List all containers (including stopped)
docker ps -a

# Pull image from registry
docker pull nodejs:18

# Search Docker Hub
docker search postgres

# View image details
docker inspect image_name

# View container logs
docker logs container_id

# Remove image
docker rmi image_id

# Remove container
docker rm container_id

# Remove unused resources
docker system prune -a
```

---

## Dockerfile & Images

### Basic Dockerfile

```dockerfile
# Dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

EXPOSE 3000

CMD ["npm", "start"]
```

### Multi-line Dockerfile Example

```dockerfile
FROM node:18-alpine

# Set working directory
WORKDIR /app

# Set environment variables
ENV NODE_ENV=production
ENV PORT=3000

# Copy dependency files
COPY package*.json ./

# Install dependencies
RUN npm ci --only=production

# Copy application files
COPY . .

# Build (if needed)
RUN npm run build

# Create non-root user for security
RUN addgroup -g 1001 -S nodejs
RUN adduser -S nextjs -u 1001

USER nextjs

# Expose port
EXPOSE 3000

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD node healthcheck.js

# Start application
CMD ["npm", "start"]
```

### Building Images

```bash
# Build image with tag
docker build -t myapp:1.0 .

# Build with multiple tags
docker build -t myapp:1.0 -t myapp:latest .

# Build with build arguments
docker build --build-arg NODE_ENV=production -t myapp:1.0 .

# Build without cache
docker build --no-cache -t myapp:1.0 .

# View build output with full history
docker build --progress=plain -t myapp:1.0 .
```

### Dockerfile Best Practices

```dockerfile
# ❌ AVOID: Large images, multiple purposes
FROM ubuntu:latest
RUN apt-get update && apt-get install -y \
    curl \
    git \
    python \
    && rm -rf /var/lib/apt/lists/*
COPY . /app
RUN cd /app && npm install

# ✅ PREFER: Minimal, single-purpose image
FROM node:18-alpine

WORKDIR /app

# Cache dependency layer
COPY package*.json ./
RUN npm ci --only=production

# Copy application
COPY . .

# Non-root user
RUN addgroup -g 1001 -S nodejs && adduser -S nodejs -u 1001
USER nodejs

EXPOSE 3000
CMD ["npm", "start"]
```

---

## Containers & Running Apps

### Running Containers

```bash
# Run container
docker run -d -p 3000:3000 --name myapp myapp:1.0

# Run with environment variables
docker run -d \
  -p 3000:3000 \
  -e NODE_ENV=production \
  -e DATABASE_URL=postgresql://user:pass@db:5432/mydb \
  --name myapp \
  myapp:1.0

# Run with volume mount
docker run -d \
  -p 3000:3000 \
  -v $(pwd):/app \
  --name myapp \
  myapp:1.0

# Run interactively
docker run -it node:18-alpine sh

# Run with resource limits
docker run -d \
  -p 3000:3000 \
  --memory=512m \
  --cpus="0.5" \
  --name myapp \
  myapp:1.0
```

### Container Management

```bash
# Stop container
docker stop container_id

# Start container
docker start container_id

# Restart container
docker restart container_id

# View container logs
docker logs container_id

# Follow logs in real-time
docker logs -f container_id

# Execute command in container
docker exec -it container_id sh

# Copy files from container
docker cp container_id:/app/file.txt ./

# Copy files to container
docker cp ./file.txt container_id:/app/

# View container stats
docker stats container_id
```

---

## Docker Compose

### Basic docker-compose.yml

```yaml
version: '3.8'

services:
  # Node.js application
  app:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: myapp
    ports:
      - "3000:3000"
    environment:
      NODE_ENV: production
      DATABASE_URL: postgresql://user:password@postgres:5432/mydb
      REDIS_URL: redis://redis:6379
    depends_on:
      - postgres
      - redis
    volumes:
      - ./src:/app/src
    networks:
      - app-network
    restart: unless-stopped

  # PostgreSQL database
  postgres:
    image: postgres:15-alpine
    container_name: myapp-postgres
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
      POSTGRES_DB: mydb
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
    networks:
      - app-network
    restart: unless-stopped
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U user"]
      interval: 10s
      timeout: 5s
      retries: 5

  # Redis cache
  redis:
    image: redis:7-alpine
    container_name: myapp-redis
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data
    networks:
      - app-network
    restart: unless-stopped

  # Nginx reverse proxy
  nginx:
    image: nginx:alpine
    container_name: myapp-nginx
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
      - ./ssl:/etc/nginx/ssl
    depends_on:
      - app
    networks:
      - app-network
    restart: unless-stopped

volumes:
  postgres_data:
  redis_data:

networks:
  app-network:
    driver: bridge
```

### Docker Compose Commands

```bash
# Start services
docker-compose up -d

# View running services
docker-compose ps

# View logs
docker-compose logs -f app

# Execute command in service
docker-compose exec app npm test

# Stop services
docker-compose stop

# Remove containers
docker-compose down

# Remove volumes
docker-compose down -v

# Rebuild images
docker-compose up -d --build
```

---

## Networking

### Network Types

```bash
# List networks
docker network ls

# Create custom network
docker network create app-network

# Run container on network
docker run -d --network app-network --name app myapp:1.0

# Connect running container to network
docker network connect app-network container_id

# Disconnect container from network
docker network disconnect app-network container_id

# Inspect network
docker network inspect app-network
```

### Service Discovery

```yaml
# docker-compose.yml - Services communicate via service name
version: '3.8'

services:
  app:
    image: myapp:1.0
    environment:
      DATABASE_URL: postgresql://user:password@postgres:5432/mydb
      REDIS_URL: redis://redis:6379
    depends_on:
      - postgres
      - redis

  postgres:
    image: postgres:15-alpine
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password

  redis:
    image: redis:7-alpine
```

---

## Volumes & Persistence

### Named Volumes

```bash
# Create volume
docker volume create myapp-data

# List volumes
docker volume ls

# Run with named volume
docker run -d -v myapp-data:/app/data myapp:1.0

# Remove volume
docker volume rm myapp-data
```

### Bind Mounts

```bash
# Mount directory
docker run -d -v $(pwd):/app myapp:1.0

# Read-only mount
docker run -d -v $(pwd):/app:ro myapp:1.0

# Windows (PowerShell)
docker run -d -v ${pwd}:/app myapp:1.0
```

### Docker Compose Volumes

```yaml
version: '3.8'

services:
  app:
    image: myapp:1.0
    volumes:
      - app-data:/app/data           # Named volume
      - ./src:/app/src               # Bind mount
      - ./config.json:/app/config.json:ro  # Read-only

  postgres:
    image: postgres:15-alpine
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./backups:/backups

volumes:
  app-data:
  postgres_data:
```

---

## Image Optimization

### Reducing Image Size

```dockerfile
# ❌ Large image (~1.2GB)
FROM ubuntu:latest
RUN apt-get update && apt-get install -y nodejs npm
COPY . /app
WORKDIR /app
RUN npm install

# ✅ Minimal image (~150MB)
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
CMD ["npm", "start"]
```

### .dockerignore File

```
# .dockerignore
node_modules
npm-debug.log
.git
.gitignore
README.md
.env.local
dist
build
coverage
.next
.nuxt
out
```

### Alpine vs Slim

```dockerfile
# Alpine (light, small - ~150MB)
FROM node:18-alpine
RUN apk add --no-cache python3

# Slim (light, includes common tools - ~200MB)
FROM node:18-slim
RUN apt-get update && apt-get install -y python3

# Full (includes build tools - ~1GB)
FROM node:18
```

---

## Multi-Stage Builds

### Reducing Final Image

```dockerfile
# Build stage
FROM node:18-alpine AS builder

WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .
RUN npm run build

# Runtime stage
FROM node:18-alpine

WORKDIR /app

# Copy only production dependencies
COPY package*.json ./
RUN npm ci --only=production

# Copy built application from builder
COPY --from=builder /app/dist ./dist

EXPOSE 3000

CMD ["npm", "start"]
```

### Next.js Multi-Stage Build

```dockerfile
# Dependencies stage
FROM node:18-alpine AS deps

WORKDIR /app
COPY package*.json ./
RUN npm ci

# Build stage
FROM node:18-alpine AS builder

WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Runtime stage
FROM node:18-alpine AS runtime

WORKDIR /app

ENV NODE_ENV production

COPY package*.json ./
RUN npm ci --only=production

# Copy built application
COPY --from=builder /app/.next ./.next
COPY --from=builder /app/public ./public

EXPOSE 3000

CMD ["npm", "start"]
```

---

## Best Practices

### Security

```dockerfile
# ✅ Use specific versions
FROM node:18.12.0-alpine

# ✅ Run as non-root user
RUN addgroup -g 1001 -S nodejs && adduser -S nodejs -u 1001
USER nodejs

# ❌ AVOID: Running as root
USER root

# ✅ Multi-stage to avoid build tools in production
# (see Multi-Stage Builds section)

# ✅ Scan images for vulnerabilities
# docker scan myapp:1.0
```

### Performance

```dockerfile
# ✅ Leverage layer caching
FROM node:18-alpine

WORKDIR /app

# Cache dependencies layer
COPY package*.json ./
RUN npm ci --only=production

# Copy application (invalidates cache if changed)
COPY . .

# ❌ AVOID: Invalidating cache too early
FROM node:18-alpine
COPY . .
RUN npm ci
```

### Logging

```bash
# Container should write logs to stdout/stderr
# NOT to files

# ✅ Good
CMD ["npm", "start"]  # Logs to stdout

# ❌ Avoid
CMD ["npm", "start", ">", "/logs/app.log"]  # Logs to file
```

### Health Checks

```dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production

COPY . .

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD curl -f http://localhost:3000/health || exit 1

EXPOSE 3000
CMD ["npm", "start"]
```

### Resource Limits

```yaml
version: '3.8'

services:
  app:
    image: myapp:1.0
    deploy:
      resources:
        limits:
          cpus: '0.5'
          memory: 512M
        reservations:
          cpus: '0.25'
          memory: 256M
```

### Do's & Don'ts

- ✅ Use alpine images for smaller size
- ✅ Implement health checks
- ✅ Set resource limits
- ✅ Use specific version tags
- ✅ Leverage multi-stage builds
- ✅ Run as non-root user
- ✅ Use environment variables for config

- ❌ Use `latest` tag in production
- ❌ Run as root
- ❌ Store secrets in images
- ❌ Include build tools in runtime
- ❌ Use large base images unnecessarily
- ❌ Ignore security warnings
- ❌ Mount production databases in dev

---

## Related Documentation

- [[Testing.md]] - Testing in containers
- [[PostgreSQL-Supabase.md]] - Database containerization
- [[Next.js/Deployment.md]] - Docker deployment

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2026-03-30 | Initial Docker & containerization guide |

