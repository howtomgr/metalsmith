# metalsmith Installation Guide

metalsmith is a free and open-source pluggable generator. Metalsmith provides extremely simple, pluggable static site generator

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
  - Storage: 1GB for sites
  - Network: Build tool
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port N/A (default metalsmith port)
  - Dev server varies
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

# Install metalsmith
sudo dnf install -y metalsmith

# Enable and start service
sudo systemctl enable --now metalsmith

# Configure firewall
sudo firewall-cmd --permanent --add-port=N/A/tcp
sudo firewall-cmd --reload

# Verify installation
metalsmith --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install metalsmith
sudo apt install -y metalsmith

# Enable and start service
sudo systemctl enable --now metalsmith

# Configure firewall
sudo ufw allow N/A

# Verify installation
metalsmith --version
```

### Arch Linux

```bash
# Install metalsmith
sudo pacman -S metalsmith

# Enable and start service
sudo systemctl enable --now metalsmith

# Verify installation
metalsmith --version
```

### Alpine Linux

```bash
# Install metalsmith
apk add --no-cache metalsmith

# Enable and start service
rc-update add metalsmith default
rc-service metalsmith start

# Verify installation
metalsmith --version
```

### openSUSE/SLES

```bash
# Install metalsmith
sudo zypper install -y metalsmith

# Enable and start service
sudo systemctl enable --now metalsmith

# Configure firewall
sudo firewall-cmd --permanent --add-port=N/A/tcp
sudo firewall-cmd --reload

# Verify installation
metalsmith --version
```

### macOS

```bash
# Using Homebrew
brew install metalsmith

# Start service
brew services start metalsmith

# Verify installation
metalsmith --version
```

### FreeBSD

```bash
# Using pkg
pkg install metalsmith

# Enable in rc.conf
echo 'metalsmith_enable="YES"' >> /etc/rc.conf

# Start service
service metalsmith start

# Verify installation
metalsmith --version
```

### Windows

```bash
# Using Chocolatey
choco install metalsmith

# Or using Scoop
scoop install metalsmith

# Verify installation
metalsmith --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/metalsmith

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
metalsmith --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable metalsmith

# Start service
sudo systemctl start metalsmith

# Stop service
sudo systemctl stop metalsmith

# Restart service
sudo systemctl restart metalsmith

# Check status
sudo systemctl status metalsmith

# View logs
sudo journalctl -u metalsmith -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add metalsmith default

# Start service
rc-service metalsmith start

# Stop service
rc-service metalsmith stop

# Restart service
rc-service metalsmith restart

# Check status
rc-service metalsmith status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'metalsmith_enable="YES"' >> /etc/rc.conf

# Start service
service metalsmith start

# Stop service
service metalsmith stop

# Restart service
service metalsmith restart

# Check status
service metalsmith status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start metalsmith
brew services stop metalsmith
brew services restart metalsmith

# Check status
brew services list | grep metalsmith
```

### Windows Service Manager

```powershell
# Start service
net start metalsmith

# Stop service
net stop metalsmith

# Using PowerShell
Start-Service metalsmith
Stop-Service metalsmith
Restart-Service metalsmith

# Check status
Get-Service metalsmith
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream metalsmith_backend {
    server 127.0.0.1:N/A;
}

server {
    listen 80;
    server_name metalsmith.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name metalsmith.example.com;

    ssl_certificate /etc/ssl/certs/metalsmith.example.com.crt;
    ssl_certificate_key /etc/ssl/private/metalsmith.example.com.key;

    location / {
        proxy_pass http://metalsmith_backend;
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
    ServerName metalsmith.example.com
    Redirect permanent / https://metalsmith.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName metalsmith.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/metalsmith.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/metalsmith.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:N/A/
    ProxyPassReverse / http://127.0.0.1:N/A/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend metalsmith_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/metalsmith.pem
    redirect scheme https if !{ ssl_fc }
    default_backend metalsmith_backend

backend metalsmith_backend
    balance roundrobin
    server metalsmith1 127.0.0.1:N/A check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R metalsmith:metalsmith /etc/metalsmith
sudo chmod 750 /etc/metalsmith

# Configure firewall
sudo firewall-cmd --permanent --add-port=N/A/tcp
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
sudo systemctl status metalsmith

# View logs
sudo journalctl -u metalsmith -f

# Monitor resource usage
top -p $(pgrep metalsmith)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/metalsmith"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/metalsmith-backup-$DATE.tar.gz" /etc/metalsmith /var/lib/metalsmith

echo "Backup completed: $BACKUP_DIR/metalsmith-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop metalsmith

# Restore from backup
tar -xzf /backup/metalsmith/metalsmith-backup-*.tar.gz -C /

# Start service
sudo systemctl start metalsmith
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u metalsmith -n 100
sudo tail -f /var/log/metalsmith/metalsmith.log

# Check configuration
metalsmith --version

# Check permissions
ls -la /etc/metalsmith
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep N/A

# Test connectivity
telnet localhost N/A

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep metalsmith)

# Check disk I/O
iotop -p $(pgrep metalsmith)

# Check connections
ss -an | grep N/A
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  metalsmith:
    image: metalsmith:latest
    ports:
      - "N/A:N/A"
    volumes:
      - ./config:/etc/metalsmith
      - ./data:/var/lib/metalsmith
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update metalsmith

# Debian/Ubuntu
sudo apt update && sudo apt upgrade metalsmith

# Arch Linux
sudo pacman -Syu metalsmith

# Alpine Linux
apk update && apk upgrade metalsmith

# openSUSE
sudo zypper update metalsmith

# FreeBSD
pkg update && pkg upgrade metalsmith

# Always backup before updates
tar -czf /backup/metalsmith-pre-update-$(date +%Y%m%d).tar.gz /etc/metalsmith

# Restart after updates
sudo systemctl restart metalsmith
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/metalsmith

# Clean old logs
find /var/log/metalsmith -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/metalsmith
```

## Additional Resources

- Official Documentation: https://docs.metalsmith.org/
- GitHub Repository: https://github.com/metalsmith/metalsmith
- Community Forum: https://forum.metalsmith.org/
- Best Practices Guide: https://docs.metalsmith.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
