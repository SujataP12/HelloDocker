# Writing Dockerfiles

Learn to create your own Docker images using Dockerfiles.

## What is a Dockerfile?

A Dockerfile is a text file containing instructions to build a Docker image. Each instruction creates a new layer in the image.

## Basic Dockerfile Structure

```dockerfile
# Comment
INSTRUCTION arguments
```

## Essential Instructions

### FROM - Base Image

Every Dockerfile must start with a FROM instruction:

```dockerfile
# Use official image
FROM ubuntu:20.04

# Use specific version
FROM node:16.14.2-alpine

# Use scratch (empty image)
FROM scratch
```

### RUN - Execute Commands

Execute commands during image build:

```dockerfile
# Single command
RUN apt-get update

# Multiple commands (shell form)
RUN apt-get update && apt-get install -y \
    curl \
    vim \
    git

# Exec form (preferred)
RUN ["apt-get", "update"]
RUN ["apt-get", "install", "-y", "curl"]
```

### COPY vs ADD

Copy files from host to image:

```dockerfile
# COPY (preferred for simple copying)
COPY src/ /app/src/
COPY package.json /app/

# ADD (has additional features)
ADD https://example.com/file.tar.gz /tmp/
ADD archive.tar.gz /app/  # Auto-extracts
```

### WORKDIR - Working Directory

Set the working directory:

```dockerfile
WORKDIR /app

# Equivalent to:
# RUN mkdir -p /app
# RUN cd /app
```

### EXPOSE - Document Ports

Document which ports the container listens on:

```dockerfile
EXPOSE 80
EXPOSE 443
EXPOSE 8080/tcp
EXPOSE 8081/udp
```

### ENV - Environment Variables

Set environment variables:

```dockerfile
ENV NODE_ENV=production
ENV PORT=3000
ENV PATH="/app/bin:${PATH}"

# Multiple variables
ENV NODE_ENV=production \
    PORT=3000 \
    DEBUG=false
```

### CMD vs ENTRYPOINT

Define what runs when container starts:

```dockerfile
# CMD - can be overridden
CMD ["npm", "start"]
CMD npm start  # Shell form

# ENTRYPOINT - always executes
ENTRYPOINT ["python", "app.py"]

# Combined usage
ENTRYPOINT ["python", "app.py"]
CMD ["--help"]  # Default argument
```

## Complete Examples

### Example 1: Simple Web Application

```dockerfile
# Use official Node.js image
FROM node:16-alpine

# Set working directory
WORKDIR /app

# Copy package files
COPY package*.json ./

# Install dependencies
RUN npm ci --only=production

# Copy application code
COPY . .

# Create non-root user
RUN addgroup -g 1001 -S nodejs
RUN adduser -S nextjs -u 1001

# Change ownership
RUN chown -R nextjs:nodejs /app
USER nextjs

# Expose port
EXPOSE 3000

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD curl -f http://localhost:3000/health || exit 1

# Start application
CMD ["npm", "start"]
```

### Example 2: Python Flask Application

```dockerfile
FROM python:3.9-slim

# Set environment variables
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

# Set work directory
WORKDIR /app

# Install system dependencies
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        postgresql-client \
    && rm -rf /var/lib/apt/lists/*

# Install Python dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy project
COPY . .

# Create user
RUN adduser --disabled-password --gecos '' appuser
RUN chown -R appuser:appuser /app
USER appuser

# Expose port
EXPOSE 8000

# Run application
CMD ["gunicorn", "--bind", "0.0.0.0:8000", "app:app"]
```

### Example 3: Multi-language Application

```dockerfile
FROM ubuntu:20.04

# Avoid prompts from apt
ENV DEBIAN_FRONTEND=noninteractive

# Install multiple runtimes
RUN apt-get update && apt-get install -y \
    python3 \
    python3-pip \
    nodejs \
    npm \
    openjdk-11-jdk \
    && rm -rf /var/lib/apt/lists/*

# Set working directory
WORKDIR /app

# Copy and install Python dependencies
COPY requirements.txt .
RUN pip3 install -r requirements.txt

# Copy and install Node.js dependencies
COPY package*.json ./
RUN npm install

# Copy Java application
COPY *.jar ./

# Copy application code
COPY . .

# Expose ports
EXPOSE 8080 3000 8000

# Default command
CMD ["python3", "main.py"]
```

## Advanced Instructions

### ARG - Build Arguments

Pass arguments during build:

```dockerfile
ARG VERSION=latest
ARG BUILD_DATE
ARG VCS_REF

FROM node:${VERSION}

LABEL build-date=${BUILD_DATE}
LABEL vcs-ref=${VCS_REF}

# Build with:
# docker build --build-arg VERSION=16 --build-arg BUILD_DATE=$(date) .
```

### LABEL - Metadata

Add metadata to images:

```dockerfile
LABEL maintainer="developer@example.com"
LABEL version="1.0"
LABEL description="My awesome application"
LABEL org.opencontainers.image.source="https://github.com/user/repo"
```

### USER - Security

Run as non-root user:

```dockerfile
# Create user
RUN groupadd -r appgroup && useradd -r -g appgroup appuser

# Switch to user
USER appuser

# Or use numeric IDs
USER 1001:1001
```

### VOLUME - Mount Points

Create mount points:

```dockerfile
VOLUME ["/data"]
VOLUME ["/var/log", "/var/db"]

# Creates anonymous volumes if not mounted
```

### HEALTHCHECK - Container Health

Define health check:

```dockerfile
HEALTHCHECK --interval=5m --timeout=3s \
  CMD curl -f http://localhost/ || exit 1

# Disable health check
HEALTHCHECK NONE
```

## Hands-on Exercises

### Exercise 1: Simple Web Server

Create a Dockerfile for a simple Python web server:

```dockerfile
FROM python:3.9-slim

WORKDIR /app

# Create a simple web server
RUN echo 'from http.server import HTTPServer, SimpleHTTPRequestHandler\n\
import os\n\
\n\
class MyHandler(SimpleHTTPRequestHandler):\n\
    def do_GET(self):\n\
        self.send_response(200)\n\
        self.send_header("Content-type", "text/html")\n\
        self.end_headers()\n\
        self.wfile.write(b"<h1>Hello from Docker!</h1>")\n\
\n\
if __name__ == "__main__":\n\
    server = HTTPServer(("0.0.0.0", 8000), MyHandler)\n\
    print("Server running on port 8000")\n\
    server.serve_forever()' > server.py

EXPOSE 8000

CMD ["python", "server.py"]
```

Build and run:
```bash
docker build -t my-web-server .
docker run -p 8000:8000 my-web-server
```

### Exercise 2: Development Environment

Create a development environment Dockerfile:

```dockerfile
FROM ubuntu:20.04

ENV DEBIAN_FRONTEND=noninteractive

# Install development tools
RUN apt-get update && apt-get install -y \
    git \
    vim \
    curl \
    wget \
    build-essential \
    python3 \
    python3-pip \
    nodejs \
    npm \
    && rm -rf /var/lib/apt/lists/*

# Install global packages
RUN npm install -g nodemon
RUN pip3 install flask requests

# Create workspace
WORKDIR /workspace

# Keep container running
CMD ["tail", "-f", "/dev/null"]
```

### Exercise 3: Multi-stage Build Preview

```dockerfile
# Build stage
FROM node:16 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Production stage
FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

## Common Patterns

### 1. Layer Optimization

```dockerfile
# Bad - creates multiple layers
RUN apt-get update
RUN apt-get install -y curl
RUN apt-get install -y vim
RUN rm -rf /var/lib/apt/lists/*

# Good - single layer
RUN apt-get update && apt-get install -y \
    curl \
    vim \
    && rm -rf /var/lib/apt/lists/*
```

### 2. Dependency Installation

```dockerfile
# Copy dependency files first
COPY package*.json ./
COPY requirements.txt ./

# Install dependencies
RUN npm ci --only=production
RUN pip install -r requirements.txt

# Copy application code last
COPY . .
```

### 3. Security Hardening

```dockerfile
# Use specific versions
FROM node:16.14.2-alpine

# Create non-root user
RUN addgroup -g 1001 -S nodejs
RUN adduser -S nextjs -u 1001

# Remove unnecessary packages
RUN apk del .build-deps

# Use non-root user
USER nextjs
```

## Best Practices Summary

1. **Use official base images**
2. **Use specific tags, not `latest`**
3. **Minimize layers**
4. **Use `.dockerignore`**
5. **Don't run as root**
6. **Use COPY instead of ADD**
7. **Leverage build cache**
8. **Keep images small**
9. **Use health checks**
10. **Document your Dockerfile**

## Next Steps

1. Learn [Best Practices](../best-practices/) in detail
2. Master [Multi-stage Builds](../multi-stage-builds/)
3. Explore [Container Networking](../../03-containers/networking/)

You're now ready to create your own Docker images! 🎉
