# Ollama Server Configuration Guide

This guide provides detailed information on configuring your Ollama server for optimal performance, remote access, and persistence.

## Remote Access Configuration

### Basic Network Configuration

By default, Ollama binds to `localhost:11434`, which only allows connections from the local machine. To enable remote access:

#### Docker Approach

```powershell
# Run docker with host binding to all interfaces
docker run -d -p 0.0.0.0:11434:11434 --name ollama-server ollama-server
```

#### WSL2 Approach

```bash
# Add to your ~/.bashrc or ~/.profile
export OLLAMA_HOST=0.0.0.0:11434

# Then restart Ollama
ollama serve
```

### Firewall Configuration

For Windows hosts, configure the firewall to allow incoming connections:

```powershell
# PowerShell (Admin)
New-NetFirewallRule -DisplayName "Ollama Server" -Direction Inbound -LocalPort 11434 -Protocol TCP -Action Allow
```

### Setting Up a Reverse Proxy (Optional)

For advanced setups with SSL/TLS encryption, you can use nginx or Caddy as a reverse proxy:

```nginx
# Example Nginx configuration
server {
    listen 443 ssl;
    server_name ollama.yourdomain.com;

    ssl_certificate /path/to/certificate.crt;
    ssl_certificate_key /path/to/privatekey.key;

    location / {
        proxy_pass http://localhost:11434;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

## Performance Tuning

### Docker Resource Allocation

Optimize Docker Desktop resource allocation:

1. Open Docker Desktop
2. Go to Settings > Resources
3. Recommended settings for Teri model:
   - CPUs: At least 4
   - Memory: 8GB or more
   - Swap: 1GB
   - Disk image size: 20GB or more

### WSL2 Resource Optimization

Create a `.wslconfig` file in your Windows user directory (`C:\Users\<YourUsername>\.wslconfig`):

```ini
[wsl2]
memory=8GB
processors=4
swap=2GB
```

### Model Optimization

Configure the Teri model for your hardware:

```bash
# In Docker container or WSL2
export OLLAMA_MODEL_PATH=/path/to/models
```

## Persistence Configuration

### Docker Volume for Model Storage

```powershell
# Create a volume for Ollama
docker volume create ollama-models

# Run with volume mounted
docker run -d -p 11434:11434 -v ollama-models:/root/.ollama --name ollama-server ollama-server
```

### WSL2 Persistence

Ollama in WSL2 automatically stores models in the `~/.ollama` directory. For additional backup:

```bash
# Backup models
sudo tar -czf ollama-backup.tar.gz ~/.ollama

# Restore models
sudo tar -xzf ollama-backup.tar.gz -C /
```

## Automated Startup

### Docker Auto-Start

```powershell
# Configure container to start with Docker
docker update --restart unless-stopped ollama-server
```

### WSL2 Systemd Service

Create a systemd service file in WSL2:

```bash
# Create service file
sudo nano /etc/systemd/system/ollama.service

# Add the following content:
[Unit]
Description=Ollama Server
After=network.target

[Service]
Environment="OLLAMA_HOST=0.0.0.0:11434"
ExecStart=/usr/local/bin/ollama serve
Restart=always
User=your_username

[Install]
WantedBy=multi-user.target

# Enable and start the service
sudo systemctl enable ollama
sudo systemctl start ollama
```

## Remote API Usage Examples

Test your remote Ollama deployment:

```bash
# List available models
curl http://your-server-ip:11434/api/tags

# Generate text with Teri model
curl -X POST http://your-server-ip:11434/api/generate -d '{
  "model": "teri",
  "prompt": "Explain how large language models work"
}'
```

