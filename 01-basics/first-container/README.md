# Your First Docker Container

Let's run your first Docker container and understand what's happening behind the scenes.

## The Hello World Container

```bash
docker run hello-world
```

### What just happened?

1. Docker looked for the `hello-world` image locally
2. Since it wasn't found, Docker pulled it from Docker Hub
3. Docker created a new container from the image
4. The container executed and displayed a message
5. The container stopped automatically

## Understanding the Output

```
Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.
```

## Interactive Container

Let's run an interactive container:

```bash
# Run Ubuntu container interactively
docker run -it ubuntu bash

# Inside the container, try these commands:
ls
cat /etc/os-release
whoami
exit
```

### Command Breakdown
- `docker run`: Create and start a container
- `-it`: Interactive mode with pseudo-TTY
- `ubuntu`: Image name
- `bash`: Command to run inside container

## Running a Web Server

```bash
# Run nginx web server
docker run -d -p 8080:80 --name my-nginx nginx

# Check if it's running
docker ps

# Test the web server
curl http://localhost:8080
# Or open http://localhost:8080 in your browser

# View logs
docker logs my-nginx

# Stop the container
docker stop my-nginx

# Remove the container
docker rm my-nginx
```

### Command Breakdown
- `-d`: Run in detached mode (background)
- `-p 8080:80`: Map host port 8080 to container port 80
- `--name my-nginx`: Give the container a name

## Container Lifecycle

```bash
# Create a container without starting it
docker create --name my-container ubuntu

# Start the container
docker start my-container

# Stop the container
docker stop my-container

# Restart the container
docker restart my-container

# Remove the container
docker rm my-container
```

## Hands-on Exercise

### Exercise 1: Run Different Containers

```bash
# 1. Run Python container and execute a command
docker run python:3.9 python -c "print('Hello from Python container!')"

# 2. Run Node.js container interactively
docker run -it node:16 node

# Inside Node.js REPL:
console.log('Hello from Node.js container!')
.exit

# 3. Run Alpine Linux (lightweight)
docker run -it alpine sh

# Inside Alpine:
apk add curl
curl -s https://httpbin.org/ip
exit
```

### Exercise 2: Container with Volume

```bash
# Create a directory on host
mkdir ~/docker-test
echo "Hello from host!" > ~/docker-test/message.txt

# Run container with volume mount
docker run -it -v ~/docker-test:/data ubuntu bash

# Inside container:
ls /data
cat /data/message.txt
echo "Hello from container!" > /data/container-message.txt
exit

# Check on host
cat ~/docker-test/container-message.txt
```

## Key Concepts Learned

1. **Images vs Containers**
   - Image: Template/blueprint
   - Container: Running instance of an image

2. **Container States**
   - Created, Running, Stopped, Removed

3. **Port Mapping**
   - Expose container ports to host

4. **Volume Mounting**
   - Share data between host and container

5. **Interactive vs Detached Mode**
   - `-it` for interactive
   - `-d` for background

## Common Commands Summary

```bash
# Run container
docker run [OPTIONS] IMAGE [COMMAND]

# List running containers
docker ps

# List all containers
docker ps -a

# Stop container
docker stop CONTAINER

# Remove container
docker rm CONTAINER

# View logs
docker logs CONTAINER

# Execute command in running container
docker exec -it CONTAINER COMMAND
```

## Next Steps

Now that you've run your first containers:
1. Learn more [Basic Commands](../basic-commands/)
2. Understand Docker images better
3. Start creating your own containers

## Troubleshooting

### Container won't start
```bash
# Check container logs
docker logs CONTAINER_NAME

# Inspect container
docker inspect CONTAINER_NAME
```

### Port already in use
```bash
# Find what's using the port
sudo netstat -tulpn | grep :8080

# Use a different port
docker run -p 8081:80 nginx
```

Great job! You've successfully run your first Docker containers! 🎉
