# 🚀 Overview
This repository contains configuration files and documentation for setting up a Coder development environment server using Docker Compose.

## 🔧 Components

### 1. Cloudflare Tunnel ☁️🛡️
- Securely exposes local services to the internet via Cloudflare
- Token-managed configuration (no local certs required)
- Replaces Traefik in this setup
- Domain routing managed in Cloudflare dashboard (e.g., `coder.hungngquang.xyz`)

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

   # Install Docker Compose plugin (preferred)
   sudo apt install -y docker-compose-plugin

   # Add your user to docker group (optional)
   sudo usermod -aG docker $USER
   ```

2. 📋 Clone this repository:
   ```bash
   git clone https://github.com/yourusername/coder.git
   cd coder
   ```

3. 🔧 Configure environment variables:
   Create a `.env` file in the project root:
   ```bash
   POSTGRES_USER=your_username
   POSTGRES_PASSWORD=your_secure_password
   POSTGRES_DB=coder
   # Docker group ID on host (e.g., from `getent group docker`)
   DOCKER_GROUP_ID=988
   # Cloudflare token for your tunnel (from Cloudflare dashboard)
   CLOUDFLARE_TUNNEL_TOKEN=xxxxxxxxxxxxxxxxxxxxxxxx
   ```

4. 📁 Prepare data storage (btrfs mount):
   ```bash
   # Create directory structure on your btrfs mount
   sudo mkdir -p /data/coder/{postgresql,netdata/{config,lib,cache},workspaces}
   
   # Set proper ownership for containers
   sudo chown -R 1000:1000 /data/coder/netdata
   sudo chown -R 999:999 /data/coder/postgresql
   
   # Create netdata cloud config placeholder
   echo "# Netdata Cloud configuration" | sudo tee /data/coder/netdata/cloud.conf
   sudo chown hungnguyen:hungnguyen /data/coder/netdata/cloud.conf
   ```

5. 🏃 Start all services:
   ```bash
   # Start all containers in detached mode (and remove orphans)
   docker compose up -d --remove-orphans
   ```

6. 🌐 Access the services:
   - 💻 Coder: https://coder.hungngquang.xyz (via Cloudflare Tunnel) or http://localhost:3000
   - 📊 Netdata: http://localhost:19999 (optional external exposure via Cloudflare if configured)
   - 📈 Coder Prometheus Metrics: http://localhost:2112

7. 🔄 **Auto-start on Boot (Optional)**: 
   ```bash
   # Create systemd service for auto-start
   sudo cp coder.service /etc/systemd/system/
   sudo systemctl daemon-reload
   sudo systemctl enable coder.service
   
   # Start the service
   sudo systemctl start coder.service
   
   # Check status
   sudo systemctl status coder.service
   ```

## 📁 Data Storage Structure

All persistent data is stored on the btrfs mount at `/data/coder/`:

```
/data/coder/
├── postgresql/          # PostgreSQL database files
├── netdata/
│   ├── config/          # Netdata configuration
│   ├── lib/             # Netdata database
│   ├── cache/           # Netdata cache
│   └── cloud.conf       # Netdata cloud configuration
└── workspaces/          # Coder workspace data (if needed)
```

This structure ensures all data is stored on your btrfs filesystem for better performance and data integrity.

## 🔒 Domain & Cloudflare Configuration

This setup uses a Cloudflare Tunnel token. Configure routing in the Cloudflare dashboard to map your hostname(s) to the tunnel:
- `coder.hungngquang.xyz` → Service `http://localhost:3000` (inside tunnel)
- Optionally add `netdata.hungngquang.xyz` → Service `http://localhost:19999`

### DNS Configuration
When using a token-managed tunnel, DNS is handled by Cloudflare automatically. You do not need to expose your server IP publicly; Cloudflare creates the necessary records.

### SSL/TLS Setup
TLS is terminated at Cloudflare when using Cloudflare Tunnel. Keep `CODER_TLS_ENABLE` disabled in the container.

## ⚙️ Configuration

### Environment Variables
The main configuration uses these environment variables:
- `POSTGRES_USER` - Database username (default: username)
- `POSTGRES_PASSWORD` - Database password (default: password)
- `POSTGRES_DB` - Database name (default: coder)
 - `DOCKER_GROUP_ID` - Host Docker group id for socket access
 - `CLOUDFLARE_TUNNEL_TOKEN` - Cloudflare tunnel token

### Coder Features Enabled
- Prometheus metrics on port 2112
- Path app sharing (for development)
- Docker socket access for workspace management

### Restart Policy
All services are configured with `restart: always` to ensure they automatically restart if they crash or if the system reboots. This works in conjunction with the systemd service for reliable auto-start behavior.

### Netdata Configuration
- Host metrics collection enabled
- Cloud integration support
- Persistent configuration storage

## 🛠️ Maintenance

### Regular Tasks
- 💾 Regular backups of PostgreSQL data (stored in `/data/coder/postgresql`)
- 📈 Monitor system resources using Netdata
- 🔒 Keep images updated for security patches
- 🔄 Restart services:
  ```bash
  docker compose restart
  ```

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
docker compose pull

# Update and restart services
docker compose up -d --remove-orphans
```

### Systemd Service Management
If you've set up the systemd service for auto-start:

```bash
# Check service status
sudo systemctl status coder.service

# Start/stop the service
sudo systemctl start coder.service
sudo systemctl stop coder.service

# Restart the service
sudo systemctl restart coder.service

# View service logs
sudo journalctl -u coder.service -f
```

## 📦 Version Management
Service versions are managed in the `docker-compose.yaml` file:
- Netdata: **v1.44.2** 
- Coder: **v2.25.1**
- PostgreSQL: **16.2**

To update versions:
1. Edit the image tags in `docker-compose.yaml`
2. Fetch new images:
   ```bash
   docker compose pull
   ```
3. Apply changes:
   ```bash
   docker compose up -d --remove-orphans
   ```

## 🔧 Troubleshooting

### Common Issues
1. **Port conflicts**: Ensure ports 3000, 19999 are available locally
2. **Database connection**: Check PostgreSQL health
   ```bash
   docker compose logs coder_database
   ```
3. **Permission issues**: Ensure Docker socket access
   ```bash
   sudo chmod 666 /var/run/docker.sock
   ```
4. **Cloudflare Tunnel cannot reach origin**: Ensure tunnel shares network with `coder`
   ```bash
   docker compose logs cloudflared
   ```

### Logs
```bash
# View all logs
docker compose logs

# View specific service logs
docker compose logs coder
docker compose logs netdata
docker compose logs cloudflared
```

### Health Checks
```bash
# Check service status
docker compose ps

# Check database health
docker exec coder-db pg_isready -U username -d coder
```
