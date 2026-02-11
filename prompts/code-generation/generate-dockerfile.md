---
type: code-generation
complexity: intermediate
context: technical
output_format: code
interaction_mode: single-shot
production_ready: experimental
tags: [docker, containers, deployment, devops, dockerfile]
---

# Generate Dockerfile for Application

## Purpose
Generate optimized, production-ready Dockerfiles with multi-stage builds, security hardening, and minimal image size.

## Context
Use this prompt when:
- Containerizing applications for deployment
- Need reproducible dev/prod environments
- Deploying to Kubernetes, ECS, or container platforms
- Optimizing image size and build times

---

## Prompt

You are a DevOps engineer. Generate a production-ready Dockerfile with multi-stage builds, security best practices, and optimization.

**INPUT**:
- Application type (Node.js, Python, Go, Java, etc.)
- Dependencies and build requirements
- Runtime requirements
- Optional: Deployment target (dev, staging, prod)

**GENERATE**:

1. **Multi-Stage Build**:
   - Build stage: Install deps, compile code
   - Runtime stage: Only production deps
   - Minimize final image size

2. **Security Hardening**:
   - Non-root user
   - Minimal base image (Alpine when possible)
   - No secrets in image layers
   - Security scanning

3. **Optimization**:
   - Layer caching (least→most changed)
   - .dockerignore for unnecessary files
   - Minimize layers
   - Use specific versions (no `:latest`)

4. **Best Practices**:
   - Health checks
   - Graceful shutdown
   - Proper signal handling
   - Logging to stdout/stderr

**OUTPUT FORMAT**:
```
# Dockerfile: [Application Type]

## Dockerfile
```dockerfile
[complete dockerfile]
```

## .dockerignore
```
[dockerignore file]
```

## Build Commands
```bash
# Development
[dev build command]

# Production
[prod build command]
```

## Docker Compose (optional)
```yaml
[compose file if needed]
```

## Notes
- Image size: [estimated size]
- Security: [hardening measures]
- Performance: [optimization notes]
```

**CONSTRAINTS**:
- Use specific versions (no `:latest`)
- Run as non-root user
- Multi-stage build for compiled languages
- Include health check
- Minimize image size (<500MB if possible)
- Cache dependencies separately from code

---

## Example

**Input**: Node.js Express API

**Requirements**:
- Node.js 20
- Production dependencies only in final image
- Run as non-root
- Health check endpoint at /health

**Output**:

```dockerfile
# Dockerfile: Node.js Express API

## Dockerfile

```dockerfile
# syntax=docker/dockerfile:1

# Stage 1: Build
FROM node:20-alpine AS builder

# Set working directory
WORKDIR /app

# Copy package files
COPY package*.json ./

# Install ALL dependencies (including devDependencies for build)
RUN npm ci --only=development

# Copy source code
COPY . .

# Build application (if using TypeScript)
RUN npm run build

# Run tests
RUN npm test

# Stage 2: Production
FROM node:20-alpine AS production

# Install dumb-init for proper signal handling
RUN apk add --no-cache dumb-init

# Create app user (non-root)
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001

# Set working directory
WORKDIR /app

# Copy package files
COPY package*.json ./

# Install ONLY production dependencies
RUN npm ci --only=production && \
    npm cache clean --force

# Copy built application from builder
COPY --from=builder --chown=nodejs:nodejs /app/dist ./dist

# Switch to non-root user
USER nodejs

# Expose port
EXPOSE 3000

# Add health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=40s --retries=3 \
  CMD node healthcheck.js

# Use dumb-init to handle signals properly
ENTRYPOINT ["dumb-init", "--"]

# Start application
CMD ["node", "dist/server.js"]
```

## .dockerignore

```
# Dependencies
node_modules/
npm-debug.log
yarn-error.log

# Environment
.env
.env.*
!.env.example

# Build artifacts
dist/
build/
*.log

# Git
.git/
.gitignore
.gitattributes

# Documentation
README.md
CHANGELOG.md
docs/

# Tests
test/
tests/
*.test.js
*.spec.js
coverage/
.nyc_output/

# IDE
.vscode/
.idea/
*.swp
*.swo
*~

# OS
.DS_Store
Thumbs.db

# Docker
Dockerfile*
docker-compose*.yml
.dockerignore
```

## Build Commands

```bash
# Development build
docker build -t myapp:dev --target builder .

# Production build
docker build -t myapp:latest -t myapp:1.0.0 .

# Build with build args
docker build \
  --build-arg NODE_ENV=production \
  -t myapp:latest \
  .

# Multi-platform build (for M1/ARM)
docker buildx build \
  --platform linux/amd64,linux/arm64 \
  -t myapp:latest \
  .
```

## Run Commands

```bash
# Run container
docker run -d \
  -p 3000:3000 \
  --name myapp \
  --restart unless-stopped \
  -e NODE_ENV=production \
  myapp:latest

# Run with volume (for logs)
docker run -d \
  -p 3000:3000 \
  -v $(pwd)/logs:/app/logs \
  myapp:latest

# Run with health check override
docker run -d \
  -p 3000:3000 \
  --health-cmd='curl -f http://localhost:3000/health || exit 1' \
  --health-interval=30s \
  myapp:latest
```

## Docker Compose

```yaml
version: '3.8'

services:
  app:
    build:
      context: .
      dockerfile: Dockerfile
      target: production
    image: myapp:latest
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - DATABASE_URL=postgresql://postgres:password@db:5432/myapp
    depends_on:
      db:
        condition: service_healthy
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "node", "healthcheck.js"]
      interval: 30s
      timeout: 3s
      retries: 3
      start_period: 40s

  db:
    image: postgres:15-alpine
    environment:
      - POSTGRES_PASSWORD=password
      - POSTGRES_DB=myapp
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5

volumes:
  postgres_data:
```

## Health Check Script

```javascript
// healthcheck.js
const http = require('http');

const options = {
  host: 'localhost',
  port: 3000,
  path: '/health',
  timeout: 2000,
};

const request = http.request(options, (res) => {
  if (res.statusCode === 200) {
    process.exit(0);
  } else {
    process.exit(1);
  }
});

request.on('error', (err) => {
  console.error('Health check failed:', err);
  process.exit(1);
});

request.end();
```

## Notes

**Image Size**:
- Builder stage: ~400MB (includes dev dependencies)
- Production stage: ~150MB (only runtime + prod deps)
- Using Alpine base reduces size by 2-3x vs Debian

**Security Hardening**:
- ✅ Non-root user (nodejs:nodejs with UID/GID 1001)
- ✅ Minimal base image (Alpine Linux)
- ✅ No secrets in layers (use environment variables)
- ✅ Specific Node version (not :latest)
- ✅ dumb-init for proper signal handling (PID 1 problem)

**Performance Optimizations**:
- ✅ Multi-stage build separates build and runtime
- ✅ Dependencies installed before code copy (layer caching)
- ✅ npm ci instead of npm install (faster, deterministic)
- ✅ npm cache cleaned to reduce size
- ✅ .dockerignore prevents unnecessary files

**Production Readiness**:
- ✅ Health check for orchestrators (Kubernetes, ECS)
- ✅ Graceful shutdown via dumb-init
- ✅ Restart policy (unless-stopped)
- ✅ Logging to stdout (12-factor app)

**Build Time**: ~2-3 minutes first build, ~30s with cache

---

## Usage Notes

- **When to use**: Containerizing any application for deployment
- **When NOT to use**: For local dev only (docker-compose with bind mounts better)
- **Best practice**: Build and scan for vulnerabilities before deploying
- **Iteration**: Optimize based on actual image size and build time metrics

---

## Quality Checklist

- [ ] Multi-stage build for smaller final image
- [ ] Runs as non-root user
- [ ] Uses specific base image version (not :latest)
- [ ] Dependencies cached separately from code
- [ ] .dockerignore excludes unnecessary files
- [ ] Health check included
- [ ] dumb-init or tini for signal handling
- [ ] Image size reasonable (<500MB)
- [ ] Security scanning passed (no HIGH/CRITICAL CVEs)
