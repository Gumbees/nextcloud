# Nextcloud Docker Compose Setup

This Docker Compose configuration provides a complete Nextcloud setup with the following components:

- **Nextcloud**: The main application (using LinuxServer.io image)
- **MariaDB**: Database backend
- **Redis**: Memory cache for improved performance
- **Cloudflared**: Optional tunnel for secure external access
- **Traefik**: Reverse proxy with automatic SSL certificates

## Features

- **ARM64 Compatible**: All images support ARM64 architecture
- **Environment-based Configuration**: All settings managed via `.env` file
- **Persistent Storage**: Separate volumes for config, data, and database
- **Network Security**: Isolated networks for different components
- **Automatic SSL**: Traefik integration with Let's Encrypt
- **Cron Jobs**: Automated background tasks for Nextcloud
- **Media Integration**: Optional media volume mounting

## Quick Start

1. **Copy the environment file:**
   ```bash
   cp env.example .env
   ```

2. **Edit the `.env` file:**
   - Set your domain name
   - Configure volume paths
   - Set secure passwords
   - Adjust PUID/PGID to match your user

3. **Create external networks (if they don't exist):**
   ```bash
   docker network create containers_internet
   ```

4. **Start the services:**
   ```bash
   docker-compose up -d
   ```

5. **Access Nextcloud:**
   - Navigate to `https://nextcloud.yourdomain.com`
   - Complete the initial setup wizard

## Environment Variables

### Core Settings
- `CONTAINER_NAME_PREFIX`: Prefix for all container names
- `PUID`/`PGID`: User/group IDs for file permissions
- `TZ`: Timezone setting
- `DOMAIN_NAME`: Your domain name for Traefik routing

### Storage Paths
- `CONFIG_BASE`: Base path for configuration files
- `DATA_BASE`: Base path for user data
- `MEDIA_BASE`: Base path for media files (optional)

### Database Configuration
- `NEXTCLOUD_DB_ROOT_PASSWORD`: MariaDB root password
- `NEXTCLOUD_DB_NAME`: Database name for Nextcloud
- `NEXTCLOUD_DB_USER`: Database username
- `NEXTCLOUD_DB_PASSWORD`: Database password

### Cache Settings
- `NEXTCLOUD_REDIS_PASSWORD`: Redis password

### Optional Features
- `CLOUDFLARED_TOKEN`: Cloudflare tunnel token
- `ENABLE_CLOUDFLARED`: Enable/disable Cloudflared (0 or 1)

## Volume Configuration

The setup supports flexible volume configurations:

### Local Volumes (Default)
Leave volume type variables empty for standard Docker volumes.

### NFS/SMB Mounts
Configure network storage by setting:
```bash
CONFIG_VOLUME_TYPE=nfs
CONFIG_VOLUME_OPTIONS=addr=192.168.1.100,rw,nfsvers=4
```

## Network Architecture

- **nextcloud_apps**: Internal network for service communication
- **nextcloud_database_network**: Isolated database network
- **container_internet**: External network for internet access

## Security Features

- Database isolated on separate network
- Redis password protection
- Traefik automatic SSL certificates
- Cloudflared tunnel support for secure external access

## Maintenance

### Backup
Important directories to backup:
- `${CONFIG_BASE}/nextcloud` - Nextcloud configuration
- `${DATA_BASE}/nextcloud` - User data
- `${CONFIG_BASE}/nextcloud-db` - Database files

### Updates
```bash
docker-compose pull
docker-compose up -d
```

### Logs
```bash
docker-compose logs -f nextcloud
docker-compose logs -f nextcloud_db
```

## Troubleshooting

### File Permissions
Ensure PUID/PGID match your host user:
```bash
id $(whoami)
```

### Database Connection
Check database connectivity:
```bash
docker-compose exec nextcloud_db mysql -u nextcloud -p
```

### Redis Connection
Verify Redis is working:
```bash
docker-compose exec nextcloud_redis redis-cli -a your_redis_password ping
```

## Initial Configuration

After first startup, configure Nextcloud:

1. **Database Settings:**
   - Database type: MySQL/MariaDB
   - Host: `nextcloud_db:3306`
   - Database name: `nextcloud` (or your `NEXTCLOUD_DB_NAME`)
   - Username: `nextcloud` (or your `NEXTCLOUD_DB_USER`)
   - Password: Your `NEXTCLOUD_DB_PASSWORD`

2. **Redis Cache:**
   - Host: `nextcloud_redis`
   - Port: `6379`
   - Password: Your `NEXTCLOUD_REDIS_PASSWORD`

3. **Trusted Domains:**
   Add your domain to trusted domains in Nextcloud config.

## Performance Optimization

For better performance, consider:
- Increasing MariaDB buffer pool size
- Configuring Redis as session handler
- Using SSD storage for database and cache
- Monitoring resource usage

## Support

For issues specific to this setup, check:
- Docker Compose logs
- Nextcloud admin documentation
- LinuxServer.io documentation for the Nextcloud image 