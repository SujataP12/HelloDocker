# Basic Docker Commands

Master the essential Docker commands that you'll use daily.

## Container Management Commands

### Running Containers

```bash
# Basic run
docker run IMAGE

# Run with options
docker run -d -p 8080:80 --name web-server nginx

# Run interactively
docker run -it ubuntu bash

# Run with environment variables
docker run -e ENV_VAR=value IMAGE

# Run with volume mount
docker run -v /host/path:/container/path IMAGE

# Run with resource limits
docker run --memory=512m --cpus=1 IMAGE
```

### Container Lifecycle

```bash
# List running containers
docker ps

# List all containers (including stopped)
docker ps -a

# Start a stopped container
docker start CONTAINER

# Stop a running container
docker stop CONTAINER

# Restart a container
docker restart CONTAINER

# Pause/unpause a container
docker pause CONTAINER
docker unpause CONTAINER

# Remove a container
docker rm CONTAINER

# Remove all stopped containers
docker container prune
```

### Container Information

```bash
# View container logs
docker logs CONTAINER

# Follow logs in real-time
docker logs -f CONTAINER

# Show last 10 lines
docker logs --tail 10 CONTAINER

# Inspect container details
docker inspect CONTAINER

# View container processes
docker top CONTAINER

# Show container resource usage
docker stats CONTAINER
```

### Executing Commands in Containers

```bash
# Execute command in running container
docker exec CONTAINER COMMAND

# Interactive shell in running container
docker exec -it CONTAINER bash

# Execute as specific user
docker exec -u root CONTAINER COMMAND

# Execute with environment variables
docker exec -e VAR=value CONTAINER COMMAND
```

## Image Management Commands

### Working with Images

```bash
# List local images
docker images

# Pull an image from registry
docker pull IMAGE:TAG

# Search for images on Docker Hub
docker search TERM

# Remove an image
docker rmi IMAGE

# Remove unused images
docker image prune

# Remove all unused images
docker image prune -a

# Show image history
docker history IMAGE

# Inspect image details
docker inspect IMAGE
```

### Building Images

```bash
# Build image from Dockerfile
docker build -t IMAGE_NAME .

# Build with specific Dockerfile
docker build -f Dockerfile.dev -t IMAGE_NAME .

# Build with build arguments
docker build --build-arg ARG=value -t IMAGE_NAME .

# Build without cache
docker build --no-cache -t IMAGE_NAME .
```

## System Commands

### System Information

```bash
# Show Docker system information
docker info

# Show Docker version
docker version

# Show disk usage
docker system df

# Show system events
docker events
```

### Cleanup Commands

```bash
# Remove all stopped containers
docker container prune

# Remove all unused images
docker image prune

# Remove all unused volumes
docker volume prune

# Remove all unused networks
docker network prune

# Remove everything unused
docker system prune

# Remove everything (including volumes)
docker system prune -a --volumes
```

## Network Commands

```bash
# List networks
docker network ls

# Create a network
docker network create NETWORK_NAME

# Connect container to network
docker network connect NETWORK CONTAINER

# Disconnect container from network
docker network disconnect NETWORK CONTAINER

# Remove network
docker network rm NETWORK_NAME

# Inspect network
docker network inspect NETWORK_NAME
```

## Volume Commands

```bash
# List volumes
docker volume ls

# Create a volume
docker volume create VOLUME_NAME

# Remove a volume
docker volume rm VOLUME_NAME

# Inspect volume
docker volume inspect VOLUME_NAME

# Remove unused volumes
docker volume prune
```

## Practical Examples

### Example 1: Web Development Setup

```bash
# Run a development web server
docker run -d \
  --name dev-server \
  -p 3000:3000 \
  -v $(pwd):/app \
  -w /app \
  node:16 \
  npm start

# View logs
docker logs -f dev-server

# Execute commands in the container
docker exec -it dev-server npm install express

# Stop and remove
docker stop dev-server
docker rm dev-server
```

### Example 2: Database Setup

```bash
# Run MySQL database
docker run -d \
  --name mysql-db \
  -e MYSQL_ROOT_PASSWORD=secret \
  -e MYSQL_DATABASE=myapp \
  -p 3306:3306 \
  -v mysql-data:/var/lib/mysql \
  mysql:8.0

# Connect to database
docker exec -it mysql-db mysql -u root -p

# Backup database
docker exec mysql-db mysqldump -u root -psecret myapp > backup.sql

# Stop and remove (data persists in volume)
docker stop mysql-db
docker rm mysql-db
```

### Example 3: Multi-container Application

```bash
# Create a network
docker network create app-network

# Run database
docker run -d \
  --name postgres-db \
  --network app-network \
  -e POSTGRES_PASSWORD=secret \
  postgres:13

# Run web application
docker run -d \
  --name web-app \
  --network app-network \
  -p 8080:8080 \
  -e DATABASE_URL=postgres://postgres:secret@postgres-db:5432/postgres \
  my-web-app:latest

# Check both containers
docker ps
```

## Command Cheat Sheet

### Most Used Commands
```bash
# Container operations
docker run -it IMAGE bash          # Interactive container
docker run -d -p 8080:80 IMAGE     # Background with port mapping
docker ps                          # List running containers
docker ps -a                       # List all containers
docker stop CONTAINER              # Stop container
docker rm CONTAINER                # Remove container
docker logs CONTAINER              # View logs
docker exec -it CONTAINER bash     # Shell into running container

# Image operations
docker images                      # List images
docker pull IMAGE                  # Download image
docker rmi IMAGE                   # Remove image
docker build -t NAME .             # Build image

# System cleanup
docker system prune                # Clean up unused resources
docker container prune             # Remove stopped containers
docker image prune                 # Remove unused images
```

### Useful Aliases

Add these to your shell profile:

```bash
# Container aliases
alias dps='docker ps'
alias dpsa='docker ps -a'
alias di='docker images'
alias drm='docker rm'
alias drmi='docker rmi'
alias dstop='docker stop'
alias dstart='docker start'
alias dlogs='docker logs'
alias dexec='docker exec -it'

# Cleanup aliases
alias dclean='docker system prune -f'
alias dcleanall='docker system prune -a -f --volumes'
```

## Hands-on Exercises

### Exercise 1: Container Management

```bash
# 1. Run nginx in background
docker run -d --name web-server -p 8080:80 nginx

# 2. Check if it's running
docker ps

# 3. View logs
docker logs web-server

# 4. Execute command inside
docker exec web-server ls /usr/share/nginx/html

# 5. Stop and remove
docker stop web-server
docker rm web-server
```

### Exercise 2: Image Operations

```bash
# 1. Search for Python images
docker search python

# 2. Pull specific Python version
docker pull python:3.9-slim

# 3. List images
docker images

# 4. Inspect the image
docker inspect python:3.9-slim

# 5. Remove the image
docker rmi python:3.9-slim
```

### Exercise 3: System Cleanup

```bash
# 1. Create some test containers
docker run --name test1 alpine echo "test1"
docker run --name test2 alpine echo "test2"
docker run --name test3 alpine echo "test3"

# 2. List all containers
docker ps -a

# 3. Remove all stopped containers
docker container prune

# 4. Check system disk usage
docker system df

# 5. Clean up everything
docker system prune
```

## Best Practices

1. **Always name your containers** for easier management
2. **Use specific image tags** instead of `latest`
3. **Clean up regularly** to save disk space
4. **Use `.dockerignore`** to exclude unnecessary files
5. **Limit resource usage** with `--memory` and `--cpus`
6. **Use volumes** for persistent data
7. **Check logs regularly** for debugging

## Next Steps

Now that you know the basic commands:
1. Learn about [Docker Images](../../02-images/)
2. Understand [Container Networking](../../03-containers/networking/)
3. Start [Writing Dockerfiles](../../04-dockerfile/)

You're well on your way to Docker mastery! 🐳
