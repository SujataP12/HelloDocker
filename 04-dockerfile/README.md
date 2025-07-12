# Dockerfile Mastery

Learn to write efficient, secure, and maintainable Dockerfiles.

## What You'll Learn

- Dockerfile syntax and instructions
- Best practices for writing Dockerfiles
- Multi-stage builds for optimization
- Security considerations

## Sections

1. [Writing Dockerfiles](./writing-dockerfiles/) - Learn Dockerfile syntax and instructions
2. [Best Practices](./best-practices/) - Write efficient and secure Dockerfiles
3. [Multi-stage Builds](./multi-stage-builds/) - Optimize your images

## Dockerfile Instructions Overview

### Essential Instructions
- `FROM` - Base image
- `RUN` - Execute commands
- `COPY` - Copy files from host
- `ADD` - Copy files with additional features
- `WORKDIR` - Set working directory
- `EXPOSE` - Document ports
- `CMD` - Default command
- `ENTRYPOINT` - Configure container executable

### Advanced Instructions
- `ENV` - Environment variables
- `ARG` - Build arguments
- `LABEL` - Add metadata
- `USER` - Set user
- `VOLUME` - Create mount points
- `HEALTHCHECK` - Container health check

## Quick Example

```dockerfile
FROM node:16-alpine

WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production

COPY . .

EXPOSE 3000

USER node

CMD ["npm", "start"]
```

## Time Investment

- **Estimated Time:** 5-7 hours
- **Difficulty:** Intermediate
- **Hands-on Labs:** 6

Let's build some amazing Docker images! 🏗️
