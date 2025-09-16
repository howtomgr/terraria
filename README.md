# terraria Installation Guide

terraria is a free and open-source Terraria dedicated server. Dedicated server for Terraria 2D sandbox game

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Supported Operating Systems](#supported-operating-systems)
3. [Installation](#installation)
4. [Configuration](#configuration)
5. [Service Management](#service-management)
6. [Troubleshooting](#troubleshooting)
7. [Security Considerations](#security-considerations)
8. [Performance Tuning](#performance-tuning)
9. [Backup and Restore](#backup-and-restore)
10. [System Requirements](#system-requirements)
11. [Support](#support)
12. [Contributing](#contributing)
13. [License](#license)
14. [Acknowledgments](#acknowledgments)
15. [Version History](#version-history)
16. [Appendices](#appendices)

## 1. Prerequisites

- **Hardware Requirements**:
  - CPU: 1 core minimum
  - RAM: 512MB minimum
  - Storage: 2GB for worlds
  - Network: Game protocol
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 7777 (default terraria port)
  - None
- **Dependencies**:
  - See official documentation for specific requirements
- **System Access**: root or sudo privileges required


## 2. Supported Operating Systems

This guide supports installation on:
- RHEL 8/9 and derivatives (CentOS Stream, Rocky Linux, AlmaLinux)
- Debian 11/12
- Ubuntu 20.04/22.04/24.04 LTS
- Arch Linux (rolling release)
- Alpine Linux 3.18+
- openSUSE Leap 15.5+ / Tumbleweed
- SUSE Linux Enterprise Server (SLES) 15+
- macOS 12+ (Monterey and later) 
- FreeBSD 13+
- Windows 10/11/Server 2019+ (where applicable)

## 3. Installation

### RHEL/CentOS/Rocky Linux/AlmaLinux

```bash
# Install EPEL repository if needed
sudo dnf install -y epel-release

# Install terraria
sudo dnf install -y terraria

# Enable and start service
sudo systemctl enable --now terraria

# Configure firewall
sudo firewall-cmd --permanent --add-port=7777/tcp
sudo firewall-cmd --reload

# Verify installation
terraria --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install terraria
sudo apt install -y terraria

# Enable and start service
sudo systemctl enable --now terraria

# Configure firewall
sudo ufw allow 7777

# Verify installation
terraria --version
```

### Arch Linux

```bash
# Install terraria
sudo pacman -S terraria

# Enable and start service
sudo systemctl enable --now terraria

# Verify installation
terraria --version
```

### Alpine Linux

```bash
# Install terraria
apk add --no-cache terraria

# Enable and start service
rc-update add terraria default
rc-service terraria start

# Verify installation
terraria --version
```

### openSUSE/SLES

```bash
# Install terraria
sudo zypper install -y terraria

# Enable and start service
sudo systemctl enable --now terraria

# Configure firewall
sudo firewall-cmd --permanent --add-port=7777/tcp
sudo firewall-cmd --reload

# Verify installation
terraria --version
```

### macOS

```bash
# Using Homebrew
brew install terraria

# Start service
brew services start terraria

# Verify installation
terraria --version
```

### FreeBSD

```bash
# Using pkg
pkg install terraria

# Enable in rc.conf
echo 'terraria_enable="YES"' >> /etc/rc.conf

# Start service
service terraria start

# Verify installation
terraria --version
```

### Windows

```bash
# Using Chocolatey
choco install terraria

# Or using Scoop
scoop install terraria

# Verify installation
terraria --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/terraria

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
terraria --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable terraria

# Start service
sudo systemctl start terraria

# Stop service
sudo systemctl stop terraria

# Restart service
sudo systemctl restart terraria

# Check status
sudo systemctl status terraria

# View logs
sudo journalctl -u terraria -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add terraria default

# Start service
rc-service terraria start

# Stop service
rc-service terraria stop

# Restart service
rc-service terraria restart

# Check status
rc-service terraria status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'terraria_enable="YES"' >> /etc/rc.conf

# Start service
service terraria start

# Stop service
service terraria stop

# Restart service
service terraria restart

# Check status
service terraria status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start terraria
brew services stop terraria
brew services restart terraria

# Check status
brew services list | grep terraria
```

### Windows Service Manager

```powershell
# Start service
net start terraria

# Stop service
net stop terraria

# Using PowerShell
Start-Service terraria
Stop-Service terraria
Restart-Service terraria

# Check status
Get-Service terraria
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream terraria_backend {
    server 127.0.0.1:7777;
}

server {
    listen 80;
    server_name terraria.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name terraria.example.com;

    ssl_certificate /etc/ssl/certs/terraria.example.com.crt;
    ssl_certificate_key /etc/ssl/private/terraria.example.com.key;

    location / {
        proxy_pass http://terraria_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Apache Configuration

```apache
<VirtualHost *:80>
    ServerName terraria.example.com
    Redirect permanent / https://terraria.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName terraria.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/terraria.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/terraria.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:7777/
    ProxyPassReverse / http://127.0.0.1:7777/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend terraria_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/terraria.pem
    redirect scheme https if !{ ssl_fc }
    default_backend terraria_backend

backend terraria_backend
    balance roundrobin
    server terraria1 127.0.0.1:7777 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R terraria:terraria /etc/terraria
sudo chmod 750 /etc/terraria

# Configure firewall
sudo firewall-cmd --permanent --add-port=7777/tcp
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on
```

## Database Setup

See official documentation for database configuration requirements.

## Performance Optimization

### System Tuning

```bash
# Basic system tuning
echo 'net.core.somaxconn = 65535' | sudo tee -a /etc/sysctl.conf
echo 'net.ipv4.tcp_max_syn_backlog = 65535' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

## Monitoring

### Basic Monitoring

```bash
# Check service status
sudo systemctl status terraria

# View logs
sudo journalctl -u terraria -f

# Monitor resource usage
top -p $(pgrep terraria)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/terraria"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/terraria-backup-$DATE.tar.gz" /etc/terraria /var/lib/terraria

echo "Backup completed: $BACKUP_DIR/terraria-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop terraria

# Restore from backup
tar -xzf /backup/terraria/terraria-backup-*.tar.gz -C /

# Start service
sudo systemctl start terraria
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u terraria -n 100
sudo tail -f /var/log/terraria/terraria.log

# Check configuration
terraria --version

# Check permissions
ls -la /etc/terraria
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 7777

# Test connectivity
telnet localhost 7777

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep terraria)

# Check disk I/O
iotop -p $(pgrep terraria)

# Check connections
ss -an | grep 7777
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  terraria:
    image: terraria:latest
    ports:
      - "7777:7777"
    volumes:
      - ./config:/etc/terraria
      - ./data:/var/lib/terraria
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update terraria

# Debian/Ubuntu
sudo apt update && sudo apt upgrade terraria

# Arch Linux
sudo pacman -Syu terraria

# Alpine Linux
apk update && apk upgrade terraria

# openSUSE
sudo zypper update terraria

# FreeBSD
pkg update && pkg upgrade terraria

# Always backup before updates
tar -czf /backup/terraria-pre-update-$(date +%Y%m%d).tar.gz /etc/terraria

# Restart after updates
sudo systemctl restart terraria
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/terraria

# Clean old logs
find /var/log/terraria -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/terraria
```

## Additional Resources

- Official Documentation: https://docs.terraria.org/
- GitHub Repository: https://github.com/terraria/terraria
- Community Forum: https://forum.terraria.org/
- Best Practices Guide: https://docs.terraria.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
