# 🚀 Coder - Remote Development Platform

[![Coder](https://img.shields.io/badge/Coder-00ADEF?style=for-the-badge&logo=coder&logoColor=white)](https://coder.com)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](https://opensource.org/licenses/MIT)
[![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)](https://www.docker.com/)
[![Cloudflare](https://img.shields.io/badge/Cloudflare-F38020?style=for-the-badge&logo=cloudflare&logoColor=white)](https://cloudflare.com)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-316192?style=for-the-badge&logo=postgresql&logoColor=white)](https://www.postgresql.org/)

## 📋 Overview

Coder is a powerful remote development platform that enables secure, cloud-based development environments. This repository provides a complete Docker-based setup for Coder with PostgreSQL, Netdata monitoring, and Cloudflare Tunnel integration for secure remote access.

## 🛠️ Features

- 💻 **Remote Development Workspaces** with full IDE capabilities
- 🔒 **Secure Access** via Cloudflare Tunnel with no exposed ports
- 🗄️ **PostgreSQL Database** for persistent data storage
- 📊 **Real-time Monitoring** with Netdata performance analytics
- 🐳 **Docker-based Deployment** for easy setup and management
- 🌐 **Cloudflare Tunnel Integration** for secure remote access
- 🔐 **Self-hosted Option** with full control over your data
- 📱 **Responsive Interface** for desktop and mobile devices
- 🚀 **High Performance** with optimized container configurations
- 🔧 **Automated Systemd Service** for auto-start on boot

## 🚀 Quick Start

### Automated Installation

This repository includes a comprehensive installation script that supports multiple Linux distributions:

```bash
# Download and run the installation script
curl -sSL https://raw.githubusercontent.com/hungnguyen1503/coder/main/install_coder.sh | bash
```

The script will automatically:
- ✅ Detect your operating system and architecture
- ✅ Update system packages
- ✅ Install Docker and Docker Compose if not present
- ✅ Create necessary directories with proper permissions
- ✅ Download and configure the docker-compose file
- ✅ Start Coder and supporting services
- ✅ Handle existing installations gracefully

### Manual Installation

If you prefer manual installation:

1. **Clone the repository:**
   ```bash
   git clone https://github.com/hungnguyen1503/coder.git
   cd coder
   ```

2. **Create environment file:**
   ```bash
   cp .env.example .env
   # Edit .env with your configuration
   nano .env  # or use your preferred editor
   ```

3. **Start the services:**
   ```bash
   docker-compose up -d
   ```

## 🔧 Configuration

### Environment Setup

1. **Copy the environment template:**
   ```bash
   cp .env.example .env
   ```

2. **Edit the `.env` file with your actual values:**
   ```bash
   nano .env  # or use your preferred editor
   ```

3. **Required variables to configure:**
   - `CLOUDFLARE_TUNNEL_TOKEN`: Get from [Cloudflare Dashboard](https://dash.cloudflare.com/cloudflare-one/tunnels)
   - `POSTGRES_USER`: Database username
   - `POSTGRES_PASSWORD`: Database password
   - `POSTGRES_DB`: Database name
   - `DOCKER_GROUP_ID`: Host Docker group ID for socket access

### Environment Variables

Create a `.env` file in the project root with the following variables:

> **⚠️ Security Note:** Never commit your actual `.env` file to version control. Use `.env.example` as a template and keep your real credentials secure.

| Variable | Description | Default | Required |
|----------|-------------|---------|----------|
| `CLOUDFLARE_TUNNEL_TOKEN` | Cloudflare Tunnel token for remote access | - | Yes |
| `POSTGRES_USER` | PostgreSQL database username | username | No |
| `POSTGRES_PASSWORD` | PostgreSQL database password | password | No |
| `POSTGRES_DB` | PostgreSQL database name | coder | No |
| `DOCKER_GROUP_ID` | Host Docker group ID for socket access | - | Yes |

### Docker Compose Services

The setup includes four main services:

#### Coder Service (`coder`)
- **Image:** `ghcr.io/coder/coder:v2.25.1`
- **Port:** `3000:3000`
- **Volume:** Docker socket access for workspace management
- **Features:**
  - Remote development workspaces
  - Prometheus metrics on port 2112
  - Path app sharing for development
  - Docker socket access for workspace management

#### PostgreSQL Database (`coder_database`)
- **Image:** `postgres:16.2`
- **Volume:** `/data/coder/postgresql:/var/lib/postgresql/data`
- **Features:**
  - Persistent data storage
  - Automatic health checks
  - Secure user authentication

#### Netdata Monitoring (`netdata`)
- **Image:** `netdata/netdata:v1.44.2`
- **Port:** `19999:19999`
- **Volume:** `/data/coder/netdata:/etc/netdata`
- **Features:**
  - Real-time system metrics
  - Performance analytics
  - Cloud integration support

#### Cloudflare Tunnel (`cloudflared`)
- **Image:** `cloudflare/cloudflared:latest`
- **Network:** Service mode for optimal performance
- **Features:**
  - Secure remote access
  - Automatic tunnel management
  - DNS optimization

## 📁 Project Structure

```
coder/
├── docker-compose.yaml    # Docker Compose configuration
├── install_coder.sh      # Automated installation script
├── coder.service         # Systemd service file for auto-start
├── README.md             # This file
├── .gitignore           # Git ignore rules
└── /data/coder/         # Data directory structure
    ├── postgresql/       # PostgreSQL database files
    ├── netdata/          # Netdata configuration and data
    └── workspaces/       # Coder workspace data
```

## 🌐 Access

After installation, access Coder through:

- **Local access:** http://localhost:3000/
- **Remote access:** Via Cloudflare Tunnel (configured in your tunnel settings)
- **Monitoring:** http://localhost:19999/ (Netdata)
- **Metrics:** http://localhost:2112/ (Prometheus)

## 🔧 Management Commands

### Start Services
```bash
docker-compose up -d
```

### Stop Services
```bash
docker-compose down
```

### View Logs
```bash
# All services
docker-compose logs -f

# Specific service
docker-compose logs -f coder
docker-compose logs -f netdata
docker-compose logs -f cloudflared
```

### Update Services
```bash
docker-compose pull
docker-compose up -d
```

### Backup Data
```bash
# Backup PostgreSQL data
docker exec coder-db pg_dump -U username coder > backup_$(date +%Y%m%d_%H%M%S).sql

# Backup all data
tar -czf coder_backup_$(date +%Y%m%d_%H%M%S).tar.gz /data/coder/
```

## 🔄 Auto-start on Boot

### Systemd Service Setup

For automatic startup on boot:

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

### Systemd Service Management

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

## 🛡️ Security Considerations

- **Cloudflare Tunnel:** Provides secure remote access without exposing ports
- **File Permissions:** Enforced settings file permissions for security
- **Volume Isolation:** Coder data is isolated in dedicated volumes
- **Environment Variables:** Sensitive data is stored in `.env` files (excluded from version control)
- **Docker Socket Access:** Controlled access to Docker daemon for workspace management

### Advanced Security Options

For additional security, consider these encryption methods:

#### Option 1: Docker Secrets (Production)
```bash
# Create encrypted secrets
echo "your_cloudflare_token" | docker secret create cloudflare_tunnel_token -
```

#### Option 2: GPG Encryption
```bash
# Encrypt your .env file
gpg -c .env
# This creates .env.gpg (encrypted) - commit this instead
```

#### Option 3: Ansible Vault
```bash
# Encrypt with Ansible Vault
ansible-vault encrypt .env
# Use: ansible-vault decrypt .env
```

## 📚 Documentation

For detailed Coder documentation, visit:
- [Official Coder Documentation](https://coder.com/docs)
- [API Reference](https://coder.com/docs/api)
- [Community Forum](https://community.coder.com)
- [Cloudflare Tunnel Documentation](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/)

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## 🐛 Troubleshooting

### Common Issues

1. **Port 3000 already in use:**
   ```bash
   sudo lsof -i :3000
   sudo kill -9 <PID>
   ```

2. **Permission issues with data directories:**
   ```bash
   sudo chown -R 1000:1000 /data/coder/netdata
   sudo chown -R 999:999 /data/coder/postgresql
   ```

3. **Docker service not running:**
   ```bash
   sudo systemctl start docker
   sudo systemctl enable docker
   ```

4. **Database connection issues:**
   ```bash
   docker exec coder-db pg_isready -U username -d coder
   ```

### Logs and Debugging

Check service logs:
```bash
# Docker logs
docker-compose logs coder

# Systemd logs (if using service)
sudo journalctl -u coder.service -f
```

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- Thanks to the [Coder team](https://coder.com) for creating an amazing remote development platform
- Thanks to [Cloudflare](https://cloudflare.com) for providing secure tunnel services
- Thanks to [Netdata](https://netdata.cloud) for excellent monitoring capabilities
- Thanks to all contributors who have helped improve this setup

## 📞 Support

- [GitHub Issues](https://github.com/coder/coder/issues)
- [Coder Community Forum](https://community.coder.com)
- [Coder Discord Server](https://discord.gg/coder)
- [Cloudflare Support](https://support.cloudflare.com/)

---

Made with ❤️ by the Coder community
