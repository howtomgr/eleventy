# eleventy Installation Guide

eleventy is a free and open-source static site generator. Eleventy provides simpler static site generator alternative

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
  - Port N/A (default eleventy port)
  - Dev server on 8080
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

# Install eleventy
sudo dnf install -y eleventy

# Enable and start service
sudo systemctl enable --now eleventy

# Configure firewall
sudo firewall-cmd --permanent --add-port=N/A/tcp
sudo firewall-cmd --reload

# Verify installation
eleventy --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install eleventy
sudo apt install -y eleventy

# Enable and start service
sudo systemctl enable --now eleventy

# Configure firewall
sudo ufw allow N/A

# Verify installation
eleventy --version
```

### Arch Linux

```bash
# Install eleventy
sudo pacman -S eleventy

# Enable and start service
sudo systemctl enable --now eleventy

# Verify installation
eleventy --version
```

### Alpine Linux

```bash
# Install eleventy
apk add --no-cache eleventy

# Enable and start service
rc-update add eleventy default
rc-service eleventy start

# Verify installation
eleventy --version
```

### openSUSE/SLES

```bash
# Install eleventy
sudo zypper install -y eleventy

# Enable and start service
sudo systemctl enable --now eleventy

# Configure firewall
sudo firewall-cmd --permanent --add-port=N/A/tcp
sudo firewall-cmd --reload

# Verify installation
eleventy --version
```

### macOS

```bash
# Using Homebrew
brew install eleventy

# Start service
brew services start eleventy

# Verify installation
eleventy --version
```

### FreeBSD

```bash
# Using pkg
pkg install eleventy

# Enable in rc.conf
echo 'eleventy_enable="YES"' >> /etc/rc.conf

# Start service
service eleventy start

# Verify installation
eleventy --version
```

### Windows

```bash
# Using Chocolatey
choco install eleventy

# Or using Scoop
scoop install eleventy

# Verify installation
eleventy --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/eleventy

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
eleventy --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable eleventy

# Start service
sudo systemctl start eleventy

# Stop service
sudo systemctl stop eleventy

# Restart service
sudo systemctl restart eleventy

# Check status
sudo systemctl status eleventy

# View logs
sudo journalctl -u eleventy -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add eleventy default

# Start service
rc-service eleventy start

# Stop service
rc-service eleventy stop

# Restart service
rc-service eleventy restart

# Check status
rc-service eleventy status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'eleventy_enable="YES"' >> /etc/rc.conf

# Start service
service eleventy start

# Stop service
service eleventy stop

# Restart service
service eleventy restart

# Check status
service eleventy status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start eleventy
brew services stop eleventy
brew services restart eleventy

# Check status
brew services list | grep eleventy
```

### Windows Service Manager

```powershell
# Start service
net start eleventy

# Stop service
net stop eleventy

# Using PowerShell
Start-Service eleventy
Stop-Service eleventy
Restart-Service eleventy

# Check status
Get-Service eleventy
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream eleventy_backend {
    server 127.0.0.1:N/A;
}

server {
    listen 80;
    server_name eleventy.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name eleventy.example.com;

    ssl_certificate /etc/ssl/certs/eleventy.example.com.crt;
    ssl_certificate_key /etc/ssl/private/eleventy.example.com.key;

    location / {
        proxy_pass http://eleventy_backend;
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
    ServerName eleventy.example.com
    Redirect permanent / https://eleventy.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName eleventy.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/eleventy.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/eleventy.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:N/A/
    ProxyPassReverse / http://127.0.0.1:N/A/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend eleventy_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/eleventy.pem
    redirect scheme https if !{ ssl_fc }
    default_backend eleventy_backend

backend eleventy_backend
    balance roundrobin
    server eleventy1 127.0.0.1:N/A check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R eleventy:eleventy /etc/eleventy
sudo chmod 750 /etc/eleventy

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
sudo systemctl status eleventy

# View logs
sudo journalctl -u eleventy -f

# Monitor resource usage
top -p $(pgrep eleventy)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/eleventy"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/eleventy-backup-$DATE.tar.gz" /etc/eleventy /var/lib/eleventy

echo "Backup completed: $BACKUP_DIR/eleventy-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop eleventy

# Restore from backup
tar -xzf /backup/eleventy/eleventy-backup-*.tar.gz -C /

# Start service
sudo systemctl start eleventy
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u eleventy -n 100
sudo tail -f /var/log/eleventy/eleventy.log

# Check configuration
eleventy --version

# Check permissions
ls -la /etc/eleventy
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
top -p $(pgrep eleventy)

# Check disk I/O
iotop -p $(pgrep eleventy)

# Check connections
ss -an | grep N/A
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  eleventy:
    image: eleventy:latest
    ports:
      - "N/A:N/A"
    volumes:
      - ./config:/etc/eleventy
      - ./data:/var/lib/eleventy
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update eleventy

# Debian/Ubuntu
sudo apt update && sudo apt upgrade eleventy

# Arch Linux
sudo pacman -Syu eleventy

# Alpine Linux
apk update && apk upgrade eleventy

# openSUSE
sudo zypper update eleventy

# FreeBSD
pkg update && pkg upgrade eleventy

# Always backup before updates
tar -czf /backup/eleventy-pre-update-$(date +%Y%m%d).tar.gz /etc/eleventy

# Restart after updates
sudo systemctl restart eleventy
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/eleventy

# Clean old logs
find /var/log/eleventy -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/eleventy
```

## Additional Resources

- Official Documentation: https://docs.eleventy.org/
- GitHub Repository: https://github.com/eleventy/eleventy
- Community Forum: https://forum.eleventy.org/
- Best Practices Guide: https://docs.eleventy.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
