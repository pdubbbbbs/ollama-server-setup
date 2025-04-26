# WSL2-Based Ollama Server Installation

This guide covers how to set up an Ollama server with the Teri model using Windows Subsystem for Linux 2 (WSL2).

## Prerequisites

- Windows 10 version 2004+ or Windows 11
- Hardware virtualization enabled in BIOS
- At least 8GB RAM
- Minimum 10GB free disk space

## Installation Steps

### 1. Enable WSL2 (if not already enabled)

Open PowerShell as Administrator and run:

```powershell
# Enable WSL and Virtual Machine Platform
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart

# Restart your computer
# After restart, set WSL default version to 2
wsl --set-default-version 2
```

### 2. Install Ubuntu (or preferred Linux distribution)

```powershell
# List available distros
wsl --list --online

# Install Ubuntu
wsl --install -d Ubuntu
```

### 3. Install Ollama in WSL2

Start your WSL2 instance and run:

```bash
# Update package information
sudo apt update && sudo apt upgrade -y

# Install dependencies
sudo apt install -y curl ca-certificates

# Install Ollama
curl -fsSL https://ollama.com/install.sh | sh

# Start Ollama server listening on all interfaces
ollama serve
```

### 4. Download and Run the Teri Model

In a new WSL2 terminal window:

```bash
ollama pull teri
```

### 5. Configure Network for Remote Access

By default, Ollama listens only on localhost. To enable remote access:

1. Stop the Ollama server (Ctrl+C)
2. Create or modify the Ollama environment configuration:
   ```bash
   echo 'OLLAMA_HOST=0.0.0.0:11434' >> ~/.bashrc
   source ~/.bashrc
   ```
3. Restart Ollama server:
   ```bash
   ollama serve
   ```

### 6. Configure Windows Firewall

Open PowerShell as Administrator and run:

```powershell
# Allow incoming connections to the Ollama port
New-NetFirewallRule -DisplayName "Ollama Server" -Direction Inbound -LocalPort 11434 -Protocol TCP -Action Allow
```

### 7. Verify the Installation

From your Windows host or another machine:

```powershell
# Windows host
curl http://localhost:11434/api/tags

# Another machine (replace with your Windows machine's IP)
curl http://YOUR_WINDOWS_IP:11434/api/tags
```

## Additional Configuration

For advanced WSL2 configurations like:
- Optimizing memory allocation
- Starting Ollama automatically
- Setting up systemd services

See the [Configuration Guide](configuration.md#wsl2-specific-configurations).

## Troubleshooting WSL2 Installation

If you encounter issues with the WSL2 installation, refer to the [Troubleshooting Guide](troubleshooting.md#wsl2-issues).

