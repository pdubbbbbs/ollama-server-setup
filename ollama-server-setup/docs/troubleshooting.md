# Troubleshooting Guide

This guide helps solve common issues you might encounter when setting up and running your Ollama server with the Teri model.

## Common Issues

### General Problems

#### Ollama Server Won't Start

**Symptoms:**
- Server fails to start or crashes immediately
- Error messages about port already in use

**Solutions:**
1. Check if another process is using port 11434:
   ```powershell
   # Windows PowerShell
   netstat -ano | findstr :11434
   ```
2. Verify you have sufficient permissions to run the server
3. Check system resources (RAM, disk space)

#### Model Download Fails

**Symptoms:**
- `ollama pull teri` command fails
- Network timeout or connection errors

**Solutions:**
1. Check your internet connection
2. Verify you have enough disk space (Teri model requires ~5-7GB)
3. Try with a different network connection or VPN
4. Run with verbose logging:
   ```bash
   OLLAMA_DEBUG=1 ollama pull teri
   ```

## Docker-Specific Issues

### Container Exits Immediately

**Symptoms:**
- Docker container exits right after starting
- No error messages in the default logs

**Solutions:**
1. Check container logs:
   ```powershell
   docker logs ollama-server
   ```
2. Start the container in interactive mode to see output:
   ```powershell
   docker run -it --name ollama-server-debug ollama-server
   ```
3. Verify the Dockerfile ENTRYPOINT is correct

### Networking Issues

**Symptoms:**
- Cannot connect to the server from outside the container
- curl http://localhost:11434/api/tags returns connection refused

**Solutions:**
1. Ensure port mapping is correct:
   ```powershell
   docker ps -a | findstr ollama
   ```
2. Check that Ollama is binding to 0.0.0.0 inside the container:
   ```powershell
   docker exec -it ollama-server bash -c "netstat -tulpn | grep 11434"
   ```
3. Verify Windows firewall settings

## WSL2-Specific Issues

### WSL2 Installation Problems

**Symptoms:**
- WSL2 installation fails
- Virtualization errors

**Solutions:**
1. Ensure virtualization is enabled in BIOS
2. Run this command to check WSL status:
   ```powershell
   wsl --status
   ```
3. Update WSL2 kernel:
   ```powershell
   wsl --update
   ```

### Network Connectivity Issues

**Symptoms:**
- Cannot access Ollama server running in WSL2 from Windows host
- Cannot access from external machines

**Solutions:**
1. Verify Ollama is configured to bind to all interfaces:
   ```bash
   # Inside WSL2
   export OLLAMA_HOST=0.0.0.0:11434
   ollama serve
   ```
2. Check Windows firewall settings:
   ```powershell
   Get-NetFirewallRule | Where-Object {$_.DisplayName -like "*Ollama*"}
   ```
3. Verify WSL2 network adapter is functioning:
   ```bash
   # Inside WSL2
   ip addr show eth0
   ```

## Model Issues

### Teri Model Performance Problems

**Symptoms:**
- Extremely slow responses
- High memory usage or crashes

**Solutions:**
1. Increase Docker Desktop resource allocation (memory, CPU)
2. For WSL2, create or modify .wslconfig:
   ```ini
   [wsl2]
   memory=8GB
   processors=4
   ```
3. Consider using a smaller model variant if available

## Advanced Troubleshooting

### Collecting Logs

```powershell
# Docker logs
docker logs ollama-server > ollama-logs.txt

# WSL2 logs
wsl -d Ubuntu -- bash -c "journalctl -u ollama.service" > ollama-wsl-logs.txt
```

### Debugging Network Issues

```powershell
# Test connectivity from Windows
Test-NetConnection -ComputerName localhost -Port 11434

# Check open ports
netstat -ano | findstr :11434
```

## Getting Help

If you encounter an issue not covered by this troubleshooting guide:

1. Search existing GitHub issues
2. Check the [Ollama official documentation](https://github.com/jmorganca/ollama/tree/main/docs)
3. Create a new issue in this repository with:
   - Your environment details (OS, Docker/WSL version)
   - Steps to reproduce
   - Error messages and logs
   - Screenshots if applicable

