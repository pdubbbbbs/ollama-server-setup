# Ollama Server Setup with Teri Model

A comprehensive guide for setting up an Ollama server with the Teri model using either Docker or WSL2 on Windows. This repository documents the entire process from environment setup to remote access configuration.

## Project Overview

Ollama is an open-source framework for running large language models locally. This project demonstrates how to set up Ollama as a remote server with the Teri model, making it accessible from other machines. The Teri model is a powerful language model that can be used for various natural language processing tasks.

## Quick Start Guide

1. **Choose your deployment strategy:**
   - [Docker-based installation](docs/docker-installation.md) (recommended for most users)
   - [WSL2-based installation](docs/wsl2-installation.md) (alternative for Windows users)

2. **Configure remote access:**
   - Follow the [Configuration Guide](docs/configuration.md) to expose your server securely
   - Implement [Security Best Practices](docs/security.md) before exposing to the internet

3. **Test your deployment:**
   - Verify the installation with basic API calls
   - Troubleshoot common issues using our [Troubleshooting Guide](docs/troubleshooting.md)

## Installation Options

### Docker-based Installation (Recommended)

```powershell
# Clone this repository
git clone https://github.com/yourusername/ollama-server-setup.git
cd ollama-server-setup

# Build the Docker image
docker build -t ollama-server .

# Run the container
docker run -d -p 11434:11434 --name ollama-server ollama-server

# Install the Teri model
docker exec -it ollama-server ollama pull teri
```

### WSL2-based Installation

```powershell
# Enable WSL2 if not already enabled
wsl --install
wsl --set-default-version 2

# Install Ubuntu
wsl --install -d Ubuntu

# Inside WSL2, install Ollama
curl -fsSL https://ollama.com/install.sh | sh

# Configure for remote access
echo 'OLLAMA_HOST=0.0.0.0:11434' >> ~/.bashrc
source ~/.bashrc

# Start Ollama server
ollama serve
```

For detailed instructions, see the dedicated installation guides:
- [Docker Installation Guide](docs/docker-installation.md)
- [WSL2 Installation Guide](docs/wsl2-installation.md)

## Documentation Structure

- **Installation Guides**
  - [Docker Installation](docs/docker-installation.md)
  - [WSL2 Installation](docs/wsl2-installation.md)
- **Configuration**
  - [Network and Remote Access](docs/configuration.md)
  - [Security Settings](docs/security.md)
- **Support**
  - [Troubleshooting](docs/troubleshooting.md)

## Prerequisites

- Windows 10/11 with WSL2 support
- Docker Desktop (version 28.0.4 or later) if using Docker approach
- Git for version control
- 8+ GB RAM recommended (Teri model requirements)
- SSD storage with at least 10GB free space
- Basic knowledge of command line operations

## Contributing

Contributions are welcome and greatly appreciated! Here's how you can contribute:

1. **Fork the repository**
2. **Create a feature branch**
   ```
   git checkout -b feature/your-feature-name
   ```
3. **Commit your changes**
   ```
   git commit -m "Add some feature"
   ```
4. **Push to your branch**
   ```
   git push origin feature/your-feature-name
   ```
5. **Create a Pull Request**

Please make sure to update tests and documentation as appropriate.

## License

This project is licensed under the MIT License - see the LICENSE file for details.

