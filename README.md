# 🚀 Overview
This repository contains configuration files and documentation for setting up a Coder development environment server using Docker Compose.

## 🔧 Components

### 1. Traefik 🌐
- Modern reverse proxy and load balancer
- Handles HTTP/HTTPS routing
- Features:
  * Automatic SSL/TLS certification
  * Web UI dashboard (port 8080)
  * Dynamic configuration
  * Docker integration
- Ports: 80 (HTTP), 443 (HTTPS), 8080 (Dashboard)

### 2. Netdata 📊
- Real-time performance and health monitoring
- Features:
  * System metrics collection
  * Resource usage monitoring
  * Performance analytics
  * Real-time alerts
- Port: 19999 (Web interface)

### 3. Coder Server 💻
- Remote development platform
- Features:
  * Secure remote workspaces
  * Development environment management
  * Multi-user support
  * Cloud integration
- Port: 3000 (Web interface)

### 4. PostgreSQL 🗄️
- Database backend for Coder
- Features:
  * Persistent data storage
  * Automatic health checks
  * Secure user authentication

## 🚦 Getting Started

1. 📥 Install Docker and Docker Compose:
   ```bash
   # Update package list
   sudo apt update

   # Install prerequisites
   sudo apt install -y apt-transport-https ca-certificates curl software-properties-common

   # Add Docker's official GPG key
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

   # Add Docker repository
   echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

   # Update package list again
   sudo apt update

   # Install Docker
   sudo apt install -y docker-ce docker-ce-cli containerd.io

   # Install Docker Compose
   sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
   sudo chmod +x /usr/local/bin/docker-compose

   # Add your user to docker group (optional)
   sudo usermod -aG docker $USER
   ```
2. 📋 Clone this repository:
   ```bash
   git clone https://github.com/yourusername/coder.git
   cd coder
   ```
3. 🏃 Start all services:
   ```bash
   # Start all containers in detached mode
   docker-compose up -d
   ```
4. 🌐 Access the services:
   - Coder: http://localhost:3000
   - Traefik Dashboard: http://localhost:8080
   - Netdata Monitoring: http://localhost:19999

5. 🔒 To make services accessible from the internet:

   Option 1: Cloudflare Tunnel (Recommended)
   - Install cloudflared: `brew install cloudflared` (macOS) or download from Cloudflare website
   - Login to Cloudflare: `cloudflared tunnel login`
   - Create a tunnel: `cloudflared tunnel create coder-tunnel`
   - Configure DNS for your domain in Cloudflare dashboard
   - Run tunnel: `cloudflared tunnel run --url http://localhost:3000 coder-tunnel`

   Option 2: Alternative Methods
   - Use Nginx as reverse proxy
   - Configure port forwarding on your router
   - Use other tunnel services (ngrok, Teleport, etc.)
   - Set up a VPN server

## ⚙️ Configuration
The main configuration is defined in `docker-compose.yaml`. Key configurations include:
- 🔗 Network settings
- 💾 Volume mounts
- 🔄 Service dependencies
- 🔐 Environment variables

## 🛠️ Maintenance
- 💾 Regular backups of PostgreSQL data recommended
- 📈 Monitor system resources using Netdata
- 🔒 Keep images updated for security patches

## 📦 Version Management
Service versions are managed in the `docker-compose.yaml` file:
- Traefik: **v2.10.7**
- Netdata: **v1.44.2** 
- Coder: **v2.21.3**
- PostgreSQL: **16.2**

To update versions:
1. Edit the image tags in `docker-compose.yaml`
2. Run `docker-compose pull` to fetch new images
3. Run `docker-compose up -d` to apply changes
