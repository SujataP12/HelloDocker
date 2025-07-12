# Docker Images

Learn everything about Docker images - the building blocks of containers.

## What You'll Learn

- Understanding Docker images and layers
- Working with Docker Hub and registries
- Creating and managing custom images
- Image optimization techniques

## Sections

1. [Working with Images](./working-with-images/) - Pull, push, and manage images
2. [Dockerfile Basics](./dockerfile-basics/) - Create your first Dockerfile
3. [Image Management](./image-management/) - Advanced image operations

## Key Concepts

### What is a Docker Image?

A Docker image is a lightweight, standalone, executable package that includes everything needed to run an application:
- Code
- Runtime
- System tools
- System libraries
- Settings

### Image Layers

Docker images are built in layers:
- Each instruction in Dockerfile creates a new layer
- Layers are cached and reused
- Only changed layers need to be rebuilt
- Layers are shared between images

### Image Naming

```
[REGISTRY_HOST[:PORT]/]USERNAME/REPOSITORY[:TAG]

Examples:
- nginx:latest
- ubuntu:20.04
- docker.io/library/python:3.9
- myregistry.com:5000/myapp:v1.0
```

## Quick Reference

### Essential Commands
```bash
# List images
docker images

# Pull image
docker pull IMAGE:TAG

# Build image
docker build -t NAME:TAG .

# Push image
docker push NAME:TAG

# Remove image
docker rmi IMAGE

# Inspect image
docker inspect IMAGE

# Show image history
docker history IMAGE
```

## Time Investment

- **Estimated Time:** 4-6 hours
- **Difficulty:** Beginner to Intermediate
- **Hands-on Labs:** 5

Ready to master Docker images? Let's dive in! 🚀
