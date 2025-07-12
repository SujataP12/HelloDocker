# Docker Compose

Learn to orchestrate multi-container applications with Docker Compose.

## What You'll Learn

- Docker Compose fundamentals
- Writing docker-compose.yml files
- Managing multi-service applications
- Environment configuration and secrets

## Sections

1. [Compose Basics](./compose-basics/) - Introduction to Docker Compose
2. [Multi-service Apps](./multi-service-apps/) - Build complex applications
3. [Environment Configs](./environment-configs/) - Manage different environments

## What is Docker Compose?

Docker Compose is a tool for defining and running multi-container Docker applications. With Compose, you use a YAML file to configure your application's services, networks, and volumes.

## Key Benefits

- **Simplified multi-container management**
- **Declarative configuration**
- **Environment isolation**
- **Easy scaling**
- **Development workflow optimization**

## Basic Compose File Structure

```yaml
version: '3.8'

services:
  web:
    build: .
    ports:
      - "8000:8000"
    depends_on:
      - db
    environment:
      - DATABASE_URL=postgresql://user:pass@db:5432/mydb

  db:
    image: postgres:13
    environment:
      - POSTGRES_DB=mydb
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=pass
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

## Essential Commands

```bash
# Start services
docker-compose up

# Start in background
docker-compose up -d

# Build and start
docker-compose up --build

# Stop services
docker-compose down

# View logs
docker-compose logs

# Scale services
docker-compose up --scale web=3

# Execute commands
docker-compose exec web bash
```

## Time Investment

- **Estimated Time:** 6-8 hours
- **Difficulty:** Intermediate
- **Hands-on Labs:** 7

Ready to orchestrate containers like a pro? Let's compose! 🎼
