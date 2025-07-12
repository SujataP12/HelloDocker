# Docker Troubleshooting Guide

Common Docker issues and their solutions.

## 🚨 Common Issues

### 1. Container Won't Start

**Symptoms:**
- Container exits immediately
- Error messages during startup
- Container status shows "Exited"

**Solutions:**
```bash
# Check container logs
docker logs CONTAINER_NAME

# Run container interactively to debug
docker run -it IMAGE_NAME bash

# Check if the command exists in the container
docker run -it IMAGE_NAME which command

# Verify the Dockerfile CMD/ENTRYPOINT
docker inspect IMAGE_NAME
```

### 2. Port Already in Use

**Error:** `bind: address already in use`

**Solutions:**
```bash
# Find what's using the port
sudo netstat -tulpn | grep :8080
sudo lsof -i :8080

# Use a different port
docker run -p 8081:80 nginx

# Stop the conflicting service
sudo systemctl stop apache2
sudo kill -9 PID
```

### 3. Permission Denied

**Error:** `permission denied while trying to connect to the Docker daemon socket`

**Solutions:**
```bash
# Add user to docker group
sudo usermod -aG docker $USER

# Apply group changes
newgrp docker

# Restart Docker service
sudo systemctl restart docker

# Fix socket permissions (temporary)
sudo chmod 666 /var/run/docker.sock
```

### 4. Out of Disk Space

**Error:** `no space left on device`

**Solutions:**
```bash
# Check Docker disk usage
docker system df

# Clean up unused resources
docker system prune -a --volumes

# Remove specific items
docker container prune    # Stopped containers
docker image prune -a     # Unused images
docker volume prune       # Unused volumes
docker network prune      # Unused networks

# Check system disk space
df -h
```

### 5. Image Build Failures

**Common build errors and solutions:**

#### Package Installation Fails
```dockerfile
# Update package lists first
RUN apt-get update && apt-get install -y package-name

# Clean up after installation
RUN apt-get update && apt-get install -y \
    package1 \
    package2 \
    && rm -rf /var/lib/apt/lists/*
```

#### File Not Found During COPY
```bash
# Check build context
ls -la

# Verify .dockerignore
cat .dockerignore

# Use correct relative paths
COPY ./src /app/src
```

#### Build Cache Issues
```bash
# Build without cache
docker build --no-cache -t myapp .

# Clear build cache
docker builder prune
```

### 6. Container Networking Issues

**Can't connect to container services:**

```bash
# Check if port is exposed
docker port CONTAINER_NAME

# Verify container is running
docker ps

# Test from inside container
docker exec -it CONTAINER_NAME curl localhost:8080

# Check container IP
docker inspect CONTAINER_NAME | grep IPAddress

# Test connectivity between containers
docker exec -it container1 ping container2
```

### 7. Volume Mount Problems

**File permission issues:**

```bash
# Check file ownership
ls -la /host/path

# Fix ownership
sudo chown -R $USER:$USER /host/path

# Use correct mount syntax
docker run -v /host/path:/container/path:rw image
```

**Volume not persisting data:**

```bash
# Check if volume exists
docker volume ls

# Inspect volume
docker volume inspect volume_name

# Verify mount point
docker inspect container_name | grep Mounts -A 10
```

### 8. Docker Compose Issues

**Service won't start:**

```bash
# Check compose file syntax
docker-compose config

# View service logs
docker-compose logs service_name

# Rebuild services
docker-compose up --build

# Force recreate containers
docker-compose up --force-recreate
```

**Services can't communicate:**

```bash
# Check network
docker-compose ps
docker network ls

# Test service connectivity
docker-compose exec service1 ping service2

# Verify service names in compose file
```

## 🔧 Debugging Tools

### Essential Commands

```bash
# Container inspection
docker inspect CONTAINER          # Detailed container info
docker logs CONTAINER             # Container logs
docker logs -f CONTAINER          # Follow logs
docker top CONTAINER              # Running processes
docker stats CONTAINER            # Resource usage
docker exec -it CONTAINER bash    # Interactive shell

# System information
docker info                       # Docker system info
docker version                    # Docker version
docker system df                  # Disk usage
docker system events              # System events

# Network debugging
docker network ls                 # List networks
docker network inspect NETWORK    # Network details
docker port CONTAINER             # Port mappings

# Image debugging
docker history IMAGE              # Image layers
docker inspect IMAGE              # Image details
```

### Useful Debugging Containers

```bash
# Network debugging
docker run --rm -it nicolaka/netshoot

# System debugging
docker run --rm -it --pid host --privileged ubuntu

# File system debugging
docker run --rm -it -v /:/host alpine chroot /host
```

## 🚀 Performance Issues

### Slow Container Startup

**Causes and solutions:**

1. **Large image size**
   ```dockerfile
   # Use smaller base images
   FROM alpine:latest
   FROM node:16-alpine
   
   # Multi-stage builds
   FROM node:16 AS builder
   # ... build steps
   FROM alpine:latest
   COPY --from=builder /app/dist /app
   ```

2. **Inefficient Dockerfile**
   ```dockerfile
   # Bad: Multiple RUN commands
   RUN apt-get update
   RUN apt-get install -y curl
   RUN apt-get install -y vim
   
   # Good: Combined commands
   RUN apt-get update && apt-get install -y \
       curl \
       vim \
       && rm -rf /var/lib/apt/lists/*
   ```

3. **No build cache optimization**
   ```dockerfile
   # Copy dependencies first
   COPY package*.json ./
   RUN npm install
   
   # Copy source code last
   COPY . .
   ```

### High Memory Usage

```bash
# Limit container memory
docker run --memory=512m myapp

# Monitor memory usage
docker stats

# Check for memory leaks
docker exec -it CONTAINER top
docker exec -it CONTAINER ps aux
```

### High CPU Usage

```bash
# Limit CPU usage
docker run --cpus=1.5 myapp

# Monitor CPU usage
docker stats

# Check processes
docker exec -it CONTAINER htop
```

## 🔍 Log Analysis

### Container Logs

```bash
# View all logs
docker logs CONTAINER

# Follow logs in real-time
docker logs -f CONTAINER

# Show last N lines
docker logs --tail 50 CONTAINER

# Show logs since timestamp
docker logs --since 2023-01-01T00:00:00 CONTAINER

# Show logs with timestamps
docker logs -t CONTAINER
```

### Application Logs

```bash
# Mount log directory
docker run -v /host/logs:/app/logs myapp

# Use logging driver
docker run --log-driver=json-file --log-opt max-size=10m myapp

# Send logs to syslog
docker run --log-driver=syslog myapp
```

## 🛡️ Security Issues

### Running as Root

```dockerfile
# Create non-root user
RUN addgroup -g 1001 -S nodejs
RUN adduser -S nextjs -u 1001

# Switch to non-root user
USER nextjs
```

### Exposed Secrets

```bash
# Use environment variables
docker run -e SECRET_KEY=value myapp

# Use Docker secrets (Swarm mode)
echo "secret_value" | docker secret create my_secret -

# Use external secret management
# - AWS Secrets Manager
# - HashiCorp Vault
# - Kubernetes Secrets
```

### Vulnerable Base Images

```bash
# Scan images for vulnerabilities
docker scan myapp:latest

# Use minimal base images
FROM alpine:latest
FROM scratch

# Keep images updated
docker pull ubuntu:latest
```

## 📋 Troubleshooting Checklist

### Before Asking for Help

- [ ] Check container logs: `docker logs CONTAINER`
- [ ] Verify container is running: `docker ps`
- [ ] Check Docker version: `docker version`
- [ ] Test with simple example first
- [ ] Check system resources: `docker system df`
- [ ] Review Dockerfile for errors
- [ ] Verify file permissions
- [ ] Check network connectivity
- [ ] Look for error messages in output
- [ ] Try rebuilding without cache

### Information to Include in Bug Reports

1. **Docker version** (`docker version`)
2. **Operating system** and version
3. **Complete error message**
4. **Steps to reproduce**
5. **Dockerfile** (if relevant)
6. **docker-compose.yml** (if using Compose)
7. **Container logs** (`docker logs`)
8. **System information** (`docker info`)

## 🆘 Getting Help

### Official Resources

- [Docker Documentation](https://docs.docker.com/)
- [Docker Community Forums](https://forums.docker.com/)
- [Docker GitHub Issues](https://github.com/docker/docker/issues)

### Community Resources

- [Stack Overflow](https://stackoverflow.com/questions/tagged/docker)
- [Reddit r/docker](https://reddit.com/r/docker)
- [Docker Community Slack](https://dockercommunity.slack.com/)

### Professional Support

- [Docker Support](https://www.docker.com/support/)
- [Docker Enterprise](https://www.docker.com/products/docker-enterprise/)

---

Remember: Most Docker issues have been encountered by others before. Search for error messages and check community resources first! 🔍
