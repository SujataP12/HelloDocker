# Docker Installation Guide

This guide covers Docker installation on different operating systems.

## Installation Options

### 1. Docker Desktop (Recommended for Beginners)

#### Windows
1. Download Docker Desktop from [docker.com](https://www.docker.com/products/docker-desktop)
2. Run the installer
3. Enable WSL 2 if prompted
4. Restart your computer
5. Launch Docker Desktop

#### macOS
1. Download Docker Desktop for Mac
2. Drag Docker to Applications folder
3. Launch Docker from Applications
4. Grant necessary permissions

#### Linux (Ubuntu/Debian)
```bash
# Update package index
sudo apt-get update

# Install required packages
sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release

# Add Docker's official GPG key
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# Set up repository
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker Engine
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin

# Add user to docker group (optional)
sudo usermod -aG docker $USER
```

### 2. Verify Installation

```bash
# Check Docker version
docker --version

# Run test container
docker run hello-world

# Check Docker info
docker info
```

## Post-Installation Steps

### Configure Docker to start on boot (Linux)
```bash
sudo systemctl enable docker.service
sudo systemctl enable containerd.service
```

### Test Your Installation
```bash
# Pull and run nginx
docker run -d -p 8080:80 nginx

# Check if it's running
curl http://localhost:8080

# Clean up
docker stop $(docker ps -q)
docker rm $(docker ps -aq)
```

## Troubleshooting

### Common Issues

1. **Permission denied (Linux)**
   ```bash
   sudo usermod -aG docker $USER
   newgrp docker
   ```

2. **Docker daemon not running**
   ```bash
   sudo systemctl start docker
   ```

3. **WSL 2 issues (Windows)**
   - Enable WSL 2 in Windows Features
   - Update WSL 2 kernel

## Next Steps

Once Docker is installed and verified:
1. Move to [First Container](../first-container/)
2. Learn basic Docker commands
3. Start building your first image

## Resources

- [Official Docker Installation Guide](https://docs.docker.com/get-docker/)
- [Docker Desktop Documentation](https://docs.docker.com/desktop/)
- [Post-installation steps for Linux](https://docs.docker.com/engine/install/linux-postinstall/)
