# Ollama Server Security Considerations

This guide covers security best practices for running an Ollama server with remote access.

## Network Security

### Basic Firewall Configuration

Limit access to your Ollama server by configuring your firewall to only allow connections from trusted IP addresses:

```powershell
# Windows Firewall - allow specific IP
New-NetFirewallRule -DisplayName "Ollama Server - Restricted" -Direction Inbound -LocalPort 11434 -Protocol TCP -RemoteAddress 192.168.1.0/24 -Action Allow

# Remove the general allow rule if you created it earlier
Remove-NetFirewallRule -DisplayName "Ollama Server"
```

### Using VPN for Remote Access

For the highest security when accessing remotely, set up a VPN:

1. Consider solutions like WireGuard, OpenVPN, or Tailscale
2. Configure Ollama to only listen on the VPN interface
3. This approach provides encryption and authentication

## Docker Security

### Non-Root User

Modify the Dockerfile to run Ollama as a non-root user:

```dockerfile
FROM ubuntu:22.04

# Install dependencies
RUN apt-get update && apt-get install -y \
    curl \
    ca-certificates \
    --no-install-recommends \
    && rm -rf /var/lib/apt/lists/*

# Create non-root user
RUN useradd -m -s /bin/bash ollama

# Install Ollama
RUN curl -fsSL https://ollama.com/install.sh | sh

# Set up the proper directories
RUN mkdir -p /home/ollama/.ollama && chown -R ollama:ollama /home/ollama/.ollama

# Switch to non-root user
USER ollama
WORKDIR /home/ollama

# Expose the Ollama API port
EXPOSE 11434

# Set up the entrypoint to run Ollama server
ENTRYPOINT ["ollama", "serve"]
```

### Docker Network Isolation

Create an isolated network for your Ollama container:

```powershell
# Create a dedicated network
docker network create ollama-net

# Run container in the dedicated network
docker run -d -p 127.0.0.1:11434:11434 --network ollama-net --name ollama-server ollama-server

# Only expose to external networks through a reverse proxy
```

## WSL2 Security

### Restrict Network Access

In WSL2, configure Ollama to only bind to specific interfaces:

```bash
# Bind only to WSL2 interface, not all interfaces
# First, find your WSL2 IP
WSL_IP=$(ip addr show eth0 | grep -oP '(?<=inet\s)\d+(\.\d+){3}')

# Then use that IP for Ollama
export OLLAMA_HOST="$WSL_IP:11434"
ollama serve
```

### File Permissions

Secure your Ollama installation with proper file permissions:

```bash
# Set proper permissions on Ollama directories
sudo chmod 750 ~/.ollama
sudo chmod 640 ~/.ollama/*
```

## Authentication Options

### Reverse Proxy with Basic Auth

Use Nginx with basic authentication:

```nginx
# Install necessary packages
# sudo apt install nginx apache2-utils

# Create password file
# htpasswd -c /etc/nginx/.htpasswd your_username

server {
    listen 443 ssl;
    server_name ollama.yourdomain.com;
    
    ssl_certificate /path/to/cert.pem;
    ssl_certificate_key /path/to/key.pem;
    
    location / {
        auth_basic "Restricted Access";
        auth_basic_user_file /etc/nginx/.htpasswd;
        
        proxy_pass http://localhost:11434;
        proxy_set_header Host

