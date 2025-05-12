# 🚀 Overview
This repository contains configuration files and documentation for setting up a Coder development environment server using Docker Compose.

## 🔧 Components

### 1. Traefik (v2.10.7) 🌐
- Modern reverse proxy and load balancer
- Handles HTTP/HTTPS routing
- Features:
  * Automatic SSL/TLS certification
  * Web UI dashboard (port 8080)
  * Dynamic configuration
  * Docker integration
- Ports: 80 (HTTP), 443 (HTTPS), 8080 (Dashboard)

### 2. Netdata (v1.44.2) 📊
- Real-time performance and health monitoring
- Features:
  * System metrics collection
  * Resource usage monitoring
  * Performance analytics
  * Real-time alerts
- Port: 19999 (Web interface)

### 3. Coder Server (v2.21.3) 💻
- Remote development platform
- Features:
  * Secure remote workspaces
  * Development environment management
  * Multi-user support
  * Cloud integration
- Port: 3000 (Web interface)

### 4. PostgreSQL (16.2) 🗄️
- Database backend for Coder
- Features:
  * Persistent data storage
  * Automatic health checks
  * Secure user authentication

## 🚦 Getting Started

1. 📥 Ensure Docker and Docker Compose are installed on your system
2. 📋 Clone this repository
3. 🏃 Run `docker-compose up -d` to start all services
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
