# Docker-Based Ollama Server Installation

This guide covers how to set up an Ollama server with the Teri model using Docker on Windows.

## Prerequisites

- Docker Desktop 28.0.4 or later
- At least 8GB RAM available for Docker
- Windows 10/11 with hardware virtualization enabled
- Minimum 10GB free disk space

## Installation Steps

### 1. Install Docker Desktop (if not already installed)

1. Download Docker Desktop from the [official website](https://www.docker.com/products/docker-desktop/)
2. Follow the installation wizard
3. Ensure virtualization is enabled in BIOS if you encounter issues
4. Verify installation with:
   ```powershell
   docker --version
   ```

### 2. Create Dockerfile

Create a new file named `Dockerfile` with the following content:

```dockerfile
FROM ubuntu:22.04

# Install dependencies
RUN apt-get update && apt-get install -y \
    curl \
    ca-certificates \
    --no-install-recommends \
    && rm -rf /var/lib/apt/lists/*

# Install Ollama
RUN curl -fsSL https://ollama.com/install.sh | sh

# Expose the Ollama API port
EXPOSE 11434

# Set up the entrypoint to run Ollama server
ENTRYPOINT ["/bin/bash", "-c", "ollama serve"]
```

### 3. Build the Docker Image

```powershell
docker build -t ollama-server .
```

### 4. Run the Container

```powershell
docker run -d -p 11434:11434 --name ollama-server ollama-server
```

### 5. Download and Run the Teri Model

```powershell
# Connect to the running container
docker exec -it ollama-server bash

# Inside the container, download the Teri model
ollama pull teri

# Exit the container
exit
```

### 6. Verify the Installation

```powershell
curl http://localhost:11434/api/tags
```

You should see a JSON response including the Teri model in the list of available models.

## Advanced Configuration

For advanced Docker configurations like:
- Persisting model data
- Setting resource limits
- Auto-starting on boot

See the [Configuration Guide](configuration.md).

## Troubleshooting Docker Installation

If you encounter issues with the Docker installation, refer to the [Troubleshooting Guide](troubleshooting.md#docker-issues).

