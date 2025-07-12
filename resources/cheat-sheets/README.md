# Docker Cheat Sheets

Quick reference guides for Docker commands and concepts.

## 📋 Available Cheat Sheets

1. [Docker Commands](#docker-commands-cheat-sheet)
2. [Dockerfile Instructions](#dockerfile-cheat-sheet)
3. [Docker Compose](#docker-compose-cheat-sheet)
4. [Docker Networking](#docker-networking-cheat-sheet)
5. [Docker Volumes](#docker-volumes-cheat-sheet)
6. [Troubleshooting](#troubleshooting-cheat-sheet)

---

## Docker Commands Cheat Sheet

### Container Operations
```bash
# Run container
docker run [OPTIONS] IMAGE [COMMAND]
docker run -it ubuntu bash                    # Interactive
docker run -d nginx                          # Detached
docker run -p 8080:80 nginx                  # Port mapping
docker run --name myapp nginx                # Named container
docker run -v /host:/container nginx         # Volume mount
docker run -e ENV_VAR=value nginx           # Environment variable

# Container lifecycle
docker ps                                    # List running containers
docker ps -a                                # List all containers
docker start CONTAINER                       # Start stopped container
docker stop CONTAINER                        # Stop running container
docker restart CONTAINER                     # Restart container
docker pause CONTAINER                       # Pause container
docker unpause CONTAINER                     # Unpause container
docker rm CONTAINER                          # Remove container
docker rm -f CONTAINER                       # Force remove running container

# Container information
docker logs CONTAINER                        # View logs
docker logs -f CONTAINER                     # Follow logs
docker logs --tail 50 CONTAINER             # Last 50 lines
docker inspect CONTAINER                     # Detailed info
docker top CONTAINER                         # Running processes
docker stats CONTAINER                       # Resource usage
docker exec -it CONTAINER bash              # Execute command
```

### Image Operations
```bash
# Image management
docker images                                # List images
docker pull IMAGE:TAG                        # Download image
docker push IMAGE:TAG                        # Upload image
docker rmi IMAGE                             # Remove image
docker rmi -f IMAGE                          # Force remove image
docker tag SOURCE TARGET                     # Tag image
docker history IMAGE                         # Image layers
docker inspect IMAGE                         # Image details

# Building images
docker build -t NAME:TAG .                   # Build from Dockerfile
docker build -f Dockerfile.dev .            # Custom Dockerfile
docker build --no-cache .                   # Build without cache
docker build --build-arg ARG=value .        # Build arguments
```

### System Operations
```bash
# System information
docker version                               # Docker version
docker info                                 # System information
docker system df                            # Disk usage
docker system events                        # System events

# Cleanup
docker system prune                          # Remove unused data
docker system prune -a                      # Remove all unused data
docker container prune                       # Remove stopped containers
docker image prune                          # Remove unused images
docker volume prune                         # Remove unused volumes
docker network prune                        # Remove unused networks
```

---

## Dockerfile Cheat Sheet

### Essential Instructions
```dockerfile
# Base image
FROM ubuntu:20.04
FROM node:16-alpine
FROM scratch

# Metadata
LABEL maintainer="email@example.com"
LABEL version="1.0"
LABEL description="My application"

# Working directory
WORKDIR /app

# Copy files
COPY src/ /app/src/
COPY package.json .
ADD https://example.com/file.tar.gz /tmp/

# Run commands
RUN apt-get update && apt-get install -y curl
RUN ["apt-get", "update"]

# Environment variables
ENV NODE_ENV=production
ENV PATH="/app/bin:${PATH}"

# Build arguments
ARG VERSION=latest
ARG BUILD_DATE

# Expose ports
EXPOSE 80
EXPOSE 443/tcp
EXPOSE 8080/udp

# Volumes
VOLUME ["/data"]
VOLUME ["/var/log", "/var/db"]

# User
USER 1001
USER appuser:appgroup

# Health check
HEALTHCHECK --interval=30s --timeout=3s \
  CMD curl -f http://localhost/ || exit 1

# Startup
CMD ["npm", "start"]                         # Default command
ENTRYPOINT ["python", "app.py"]             # Always executes
ENTRYPOINT ["python"] CMD ["app.py"]        # Combined
```

### Best Practices
```dockerfile
# Use specific tags
FROM node:16.14.2-alpine

# Minimize layers
RUN apt-get update && apt-get install -y \
    curl \
    vim \
    && rm -rf /var/lib/apt/lists/*

# Copy dependencies first
COPY package*.json ./
RUN npm ci --only=production
COPY . .

# Use non-root user
RUN addgroup -g 1001 -S nodejs
RUN adduser -S nextjs -u 1001
USER nextjs

# Multi-stage build
FROM node:16 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
```

---

## Docker Compose Cheat Sheet

### Basic Commands
```bash
# Start services
docker-compose up                            # Start in foreground
docker-compose up -d                         # Start in background
docker-compose up --build                    # Build and start
docker-compose up --scale web=3              # Scale service

# Stop services
docker-compose down                          # Stop and remove
docker-compose down -v                       # Remove volumes too
docker-compose stop                          # Stop services
docker-compose start                         # Start stopped services

# Service management
docker-compose ps                            # List services
docker-compose logs                          # View logs
docker-compose logs -f web                   # Follow service logs
docker-compose exec web bash                 # Execute command
docker-compose run web python manage.py migrate

# Build and push
docker-compose build                         # Build services
docker-compose push                          # Push images
docker-compose pull                          # Pull images
```

### Compose File Structure
```yaml
version: '3.8'

services:
  web:
    build: .                                 # Build from Dockerfile
    image: myapp:latest                      # Use existing image
    container_name: web-app                  # Custom name
    ports:
      - "8000:8000"                         # Port mapping
      - "127.0.0.1:8001:8001"              # Bind to specific IP
    environment:
      - DEBUG=1                             # Environment variables
      - DATABASE_URL=postgresql://user:pass@db:5432/mydb
    env_file:
      - .env                                # Environment file
    volumes:
      - .:/app                              # Bind mount
      - static_volume:/app/static           # Named volume
    depends_on:
      - db                                  # Service dependency
    networks:
      - app-network                         # Custom network
    restart: unless-stopped                 # Restart policy
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8000/health"]
      interval: 30s
      timeout: 10s
      retries: 3

  db:
    image: postgres:13
    environment:
      POSTGRES_DB: mydb
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"

volumes:
  postgres_data:
  static_volume:

networks:
  app-network:
    driver: bridge
```

---

## Docker Networking Cheat Sheet

### Network Commands
```bash
# List networks
docker network ls

# Create network
docker network create mynetwork
docker network create --driver bridge mynetwork
docker network create --subnet=172.20.0.0/16 mynetwork

# Connect/disconnect containers
docker network connect mynetwork container1
docker network disconnect mynetwork container1

# Inspect network
docker network inspect mynetwork

# Remove network
docker network rm mynetwork
docker network prune                         # Remove unused networks
```

### Network Types
```bash
# Bridge (default)
docker run --network bridge nginx

# Host (use host networking)
docker run --network host nginx

# None (no networking)
docker run --network none nginx

# Custom bridge
docker network create mybridge
docker run --network mybridge nginx

# Container networking
docker run --network container:other_container nginx
```

### Port Mapping
```bash
# Basic port mapping
docker run -p 8080:80 nginx                 # Host:Container

# Bind to specific interface
docker run -p 127.0.0.1:8080:80 nginx

# Multiple ports
docker run -p 8080:80 -p 8443:443 nginx

# UDP ports
docker run -p 8080:80/udp nginx

# Random host port
docker run -P nginx
```

---

## Docker Volumes Cheat Sheet

### Volume Commands
```bash
# List volumes
docker volume ls

# Create volume
docker volume create myvolume

# Inspect volume
docker volume inspect myvolume

# Remove volume
docker volume rm myvolume
docker volume prune                          # Remove unused volumes
```

### Volume Types
```bash
# Named volume
docker run -v myvolume:/data nginx

# Bind mount
docker run -v /host/path:/container/path nginx
docker run -v $(pwd):/app nginx             # Current directory

# Anonymous volume
docker run -v /data nginx

# Read-only mount
docker run -v /host/path:/container/path:ro nginx

# Volume with options
docker run -v myvolume:/data:rw,Z nginx
```

### Volume in Compose
```yaml
services:
  web:
    volumes:
      - type: bind
        source: ./app
        target: /app
      - type: volume
        source: mydata
        target: /data
      - type: tmpfs
        target: /tmp

volumes:
  mydata:
    driver: local
    driver_opts:
      type: nfs
      o: addr=192.168.1.1,rw
      device: ":/path/to/dir"
```

---

## Troubleshooting Cheat Sheet

### Common Issues and Solutions

#### Container Won't Start
```bash
# Check logs
docker logs CONTAINER

# Inspect container
docker inspect CONTAINER

# Check if image exists
docker images | grep IMAGE_NAME

# Run with different command
docker run -it IMAGE bash
```

#### Port Already in Use
```bash
# Find what's using the port
sudo netstat -tulpn | grep :8080
sudo lsof -i :8080

# Use different port
docker run -p 8081:80 nginx

# Stop conflicting service
sudo systemctl stop apache2
```

#### Permission Denied
```bash
# Add user to docker group
sudo usermod -aG docker $USER
newgrp docker

# Fix file permissions
sudo chown -R $USER:$USER /path/to/files

# Run as root (not recommended)
sudo docker run nginx
```

#### Out of Disk Space
```bash
# Check disk usage
docker system df

# Clean up
docker system prune -a --volumes

# Remove specific items
docker container prune
docker image prune -a
docker volume prune
```

#### Container Networking Issues
```bash
# Check container IP
docker inspect CONTAINER | grep IPAddress

# Test connectivity
docker exec CONTAINER ping google.com
docker exec CONTAINER nslookup google.com

# Check port binding
docker port CONTAINER

# Inspect network
docker network inspect bridge
```

#### Build Issues
```bash
# Build without cache
docker build --no-cache .

# Check build context
docker build --progress=plain .

# Use different Dockerfile
docker build -f Dockerfile.debug .

# Check .dockerignore
cat .dockerignore
```

### Useful Debugging Commands
```bash
# Enter running container
docker exec -it CONTAINER bash

# Copy files from container
docker cp CONTAINER:/path/file ./file

# Check container processes
docker top CONTAINER

# Monitor resource usage
docker stats

# View system events
docker events

# Get container IP
docker inspect CONTAINER | grep IPAddress
```

### Performance Tips
```bash
# Limit memory
docker run --memory=512m nginx

# Limit CPU
docker run --cpus=1.5 nginx

# Set restart policy
docker run --restart=unless-stopped nginx

# Use multi-stage builds
# See Dockerfile examples above

# Optimize image layers
# Combine RUN commands
# Use .dockerignore
# Use specific base images
```

---

## Quick Reference Cards

### Essential Commands
```bash
docker run -it IMAGE bash        # Interactive container
docker run -d -p 8080:80 IMAGE   # Background with port
docker ps                        # List containers
docker images                    # List images
docker logs CONTAINER            # View logs
docker exec -it CONTAINER bash   # Shell into container
docker stop CONTAINER            # Stop container
docker rm CONTAINER              # Remove container
docker rmi IMAGE                 # Remove image
docker system prune              # Clean up
```

### Dockerfile Essentials
```dockerfile
FROM image:tag
WORKDIR /app
COPY . .
RUN command
EXPOSE port
CMD ["command"]
```

### Compose Essentials
```yaml
version: '3.8'
services:
  app:
    build: .
    ports:
      - "8080:8080"
    volumes:
      - .:/app
```

---

Print these cheat sheets and keep them handy while learning Docker! 📋✨
