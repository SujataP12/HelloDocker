# Beginner Docker Projects

Hands-on projects to practice your Docker skills.

## Project List

### 1. Static Website with Nginx
**Difficulty:** ⭐  
**Time:** 30 minutes  
**Skills:** Basic Docker, Nginx

Create a simple static website served by Nginx.

**What you'll learn:**
- Basic Dockerfile creation
- Working with Nginx
- Port mapping
- Volume mounting

**Files to create:**
- `index.html`
- `Dockerfile`
- `nginx.conf` (optional)

### 2. Python Flask API
**Difficulty:** ⭐⭐  
**Time:** 1 hour  
**Skills:** Python, Flask, Docker

Build a simple REST API with Flask.

**What you'll learn:**
- Python application containerization
- Environment variables
- API development
- Health checks

**Files to create:**
- `app.py`
- `requirements.txt`
- `Dockerfile`

### 3. Node.js Express App
**Difficulty:** ⭐⭐  
**Time:** 1 hour  
**Skills:** Node.js, Express, Docker

Create a Node.js web application.

**What you'll learn:**
- Node.js containerization
- Package management
- Development vs production builds
- Multi-stage builds (optional)

**Files to create:**
- `package.json`
- `server.js`
- `Dockerfile`

### 4. Database with Persistent Storage
**Difficulty:** ⭐⭐  
**Time:** 45 minutes  
**Skills:** Databases, Volumes

Set up a database with persistent data.

**What you'll learn:**
- Volume management
- Database containerization
- Data persistence
- Environment configuration

**Databases to try:**
- PostgreSQL
- MySQL
- MongoDB

### 5. Multi-container Web App
**Difficulty:** ⭐⭐⭐  
**Time:** 2 hours  
**Skills:** Docker Compose, Multi-container

Build a web application with separate frontend and backend.

**What you'll learn:**
- Docker Compose basics
- Service communication
- Network configuration
- Multi-container orchestration

**Components:**
- Frontend (React/Vue/Angular)
- Backend API
- Database

## Project Templates

### Project 1: Static Website

**Directory Structure:**
```
static-website/
├── Dockerfile
├── index.html
├── style.css
└── script.js
```

**index.html:**
```html
<!DOCTYPE html>
<html>
<head>
    <title>My Docker Website</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <div class="container">
        <h1>Welcome to My Docker Website!</h1>
        <p>This website is running in a Docker container.</p>
        <button onclick="showMessage()">Click Me!</button>
        <div id="message"></div>
    </div>
    <script src="script.js"></script>
</body>
</html>
```

**Dockerfile:**
```dockerfile
FROM nginx:alpine

COPY index.html /usr/share/nginx/html/
COPY style.css /usr/share/nginx/html/
COPY script.js /usr/share/nginx/html/

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

**Commands:**
```bash
docker build -t my-website .
docker run -p 8080:80 my-website
```

### Project 2: Flask API

**Directory Structure:**
```
flask-api/
├── Dockerfile
├── app.py
├── requirements.txt
└── .dockerignore
```

**app.py:**
```python
from flask import Flask, jsonify
import os

app = Flask(__name__)

@app.route('/')
def hello():
    return jsonify({
        'message': 'Hello from Docker!',
        'environment': os.environ.get('ENVIRONMENT', 'development')
    })

@app.route('/health')
def health():
    return jsonify({'status': 'healthy'})

@app.route('/api/users')
def users():
    return jsonify({
        'users': [
            {'id': 1, 'name': 'Alice'},
            {'id': 2, 'name': 'Bob'}
        ]
    })

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000, debug=True)
```

**requirements.txt:**
```
Flask==2.3.2
```

**Dockerfile:**
```dockerfile
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install -r requirements.txt

COPY . .

EXPOSE 5000

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD curl -f http://localhost:5000/health || exit 1

CMD ["python", "app.py"]
```

### Project 3: Node.js Express

**package.json:**
```json
{
  "name": "docker-express-app",
  "version": "1.0.0",
  "description": "Express app running in Docker",
  "main": "server.js",
  "scripts": {
    "start": "node server.js",
    "dev": "nodemon server.js"
  },
  "dependencies": {
    "express": "^4.18.2"
  },
  "devDependencies": {
    "nodemon": "^2.0.22"
  }
}
```

**server.js:**
```javascript
const express = require('express');
const app = express();
const PORT = process.env.PORT || 3000;

app.use(express.json());

app.get('/', (req, res) => {
  res.json({
    message: 'Hello from Node.js Docker container!',
    timestamp: new Date().toISOString(),
    environment: process.env.NODE_ENV || 'development'
  });
});

app.get('/api/status', (req, res) => {
  res.json({
    status: 'running',
    uptime: process.uptime(),
    memory: process.memoryUsage()
  });
});

app.listen(PORT, '0.0.0.0', () => {
  console.log(`Server running on port ${PORT}`);
});
```

**Dockerfile:**
```dockerfile
FROM node:16-alpine

WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production

COPY . .

RUN addgroup -g 1001 -S nodejs
RUN adduser -S nextjs -u 1001
RUN chown -R nextjs:nodejs /app
USER nextjs

EXPOSE 3000

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD wget --no-verbose --tries=1 --spider http://localhost:3000/api/status || exit 1

CMD ["npm", "start"]
```

### Project 4: Database Setup

**PostgreSQL Example:**
```bash
# Run PostgreSQL with persistent storage
docker run -d \
  --name postgres-db \
  -e POSTGRES_DB=myapp \
  -e POSTGRES_USER=admin \
  -e POSTGRES_PASSWORD=secret123 \
  -p 5432:5432 \
  -v postgres_data:/var/lib/postgresql/data \
  postgres:13

# Connect to database
docker exec -it postgres-db psql -U admin -d myapp

# Create a table
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  name VARCHAR(100),
  email VARCHAR(100)
);

# Insert data
INSERT INTO users (name, email) VALUES 
('Alice', 'alice@example.com'),
('Bob', 'bob@example.com');

# Query data
SELECT * FROM users;
```

### Project 5: Multi-container App

**docker-compose.yml:**
```yaml
version: '3.8'

services:
  frontend:
    build: ./frontend
    ports:
      - "3000:3000"
    depends_on:
      - backend
    environment:
      - REACT_APP_API_URL=http://localhost:5000

  backend:
    build: ./backend
    ports:
      - "5000:5000"
    depends_on:
      - database
    environment:
      - DATABASE_URL=postgresql://admin:secret@database:5432/myapp
      - NODE_ENV=development

  database:
    image: postgres:13
    environment:
      - POSTGRES_DB=myapp
      - POSTGRES_USER=admin
      - POSTGRES_PASSWORD=secret
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"

volumes:
  postgres_data:
```

## Getting Started

1. **Choose a project** based on your skill level
2. **Create a new directory** for your project
3. **Follow the template** or create your own variation
4. **Build and run** your Docker containers
5. **Test your application**
6. **Document your learnings**

## Tips for Success

1. **Start simple** - Don't try to build everything at once
2. **Read error messages** - Docker provides helpful error information
3. **Use docker logs** - Check container logs when things don't work
4. **Experiment** - Try different configurations and options
5. **Document** - Keep notes of what you learn
6. **Share** - Show your projects to others for feedback

## Next Steps

After completing these projects:
1. Move to [Intermediate Projects](../intermediate-projects/)
2. Learn about [Docker Compose](../../05-compose/)
3. Explore [Production Deployment](../../06-production/)

## Resources

- [Docker Hub](https://hub.docker.com/) - Find base images
- [Dockerfile Reference](https://docs.docker.com/engine/reference/builder/)
- [Docker Compose Reference](https://docs.docker.com/compose/compose-file/)

Happy coding! 🚀
