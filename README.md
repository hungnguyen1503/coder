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
  * Cloud integration support
- Port: 19999 (Web interface)
- Domain: netdata.hungngquang.xyz

### 3. Coder Server 💻
- Remote development platform
- Features:
  * Secure remote workspaces
  * Development environment management
  * Multi-user support
  * Cloud integration
  * Prometheus metrics enabled
  * Auto-fill parameters experiment
- Port: 3000 (Web interface)
- Domain: coder.hungngquang.xyz
- Prometheus metrics: 2112

### 4. PostgreSQL 🗄️
- Database backend for Coder
- Features:
  * Persistent data storage
  * Automatic health checks
  * Secure user authentication
- Version: 16.2

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

3. 🔧 Configure environment variables (optional):
   Create a `.env` file in the project root:
   ```bash
   POSTGRES_USER=your_username
   POSTGRES_PASSWORD=your_secure_password
   POSTGRES_DB=coder
   ```

4. 🏃 Start all services:
   ```bash
   # Start all containers in detached mode
   docker-compose up -d
   ```

5. 🌐 Access the services:
   - Coder: http://localhost:3000 or https://coder.hungngquang.xyz
   - Traefik Dashboard: http://localhost:8080
   - Netdata Monitoring: http://localhost:19999 or https://netdata.hungngquang.xyz
   - Coder Prometheus Metrics: http://localhost:2112

## 🔒 Domain Configuration

This setup is configured for the domain `hungngquang.xyz` with the following subdomains:
- `coder.hungngquang.xyz` - Coder development platform
- `netdata.hungngquang.xyz` - System monitoring dashboard

### DNS Configuration
Ensure your DNS records point to your server:
- `coder.hungngquang.xyz` → Your server IP
- `netdata.hungngquang.xyz` → Your server IP

### SSL/TLS Setup
The current configuration has TLS disabled for local development. For production:

1. Enable TLS in the Coder environment variables:
   ```yaml
   CODER_TLS_ENABLE: "true"
   CODER_TLS_REDIRECT_HTTP: "true"
   ```

2. Configure SSL certificates through Traefik or your preferred method.

## ⚙️ Configuration

### Environment Variables
The main configuration uses these environment variables:
- `POSTGRES_USER` - Database username (default: username)
- `POSTGRES_PASSWORD` - Database password (default: password)
- `POSTGRES_DB` - Database name (default: coder)

### Coder Features Enabled
- Prometheus metrics on port 2112
- Auto-fill parameters experiment
- Path app sharing (for development)
- Docker socket access for workspace management

### Netdata Configuration
- Host metrics collection enabled
- Cloud integration support
- Persistent configuration storage

## 🛠️ Maintenance

### Regular Tasks
- 💾 Regular backups of PostgreSQL data (stored in `coder_data` volume)
- 📈 Monitor system resources using Netdata
- 🔒 Keep images updated for security patches
- 🔄 Restart services: `docker-compose restart`

### Backup Database
```bash
# Create backup
docker exec coder-db pg_dump -U username coder > backup_$(date +%Y%m%d_%H%M%S).sql

# Restore backup
docker exec -i coder-db psql -U username coder < backup_file.sql
```

### Update Services
```bash
# Pull latest images
docker-compose pull

# Update and restart services
docker-compose up -d
```

## 📦 Version Management
Service versions are managed in the `docker-compose.yaml` file:
- Traefik: **v2.10.7**
- Netdata: **v1.44.2** 
- Coder: **v2.25.1** ⚠️ Updated from v2.21.3
- PostgreSQL: **16.2**

To update versions:
1. Edit the image tags in `docker-compose.yaml`
2. Run `docker-compose pull` to fetch new images
3. Run `docker-compose up -d` to apply changes

## 🔧 Troubleshooting

### Common Issues
1. **Port conflicts**: Ensure ports 80, 443, 3000, 8080, 19999 are available
2. **Database connection**: Check PostgreSQL health with `docker-compose logs coder_database`
3. **Permission issues**: Ensure Docker socket access: `sudo chmod 666 /var/run/docker.sock`

### Logs
```bash
# View all logs
docker-compose logs

# View specific service logs
docker-compose logs coder
docker-compose logs netdata
docker-compose logs traefik
```

### Health Checks
```bash
# Check service status
docker-compose ps

# Check database health
docker exec coder-db pg_isready -U username -d coder
```
