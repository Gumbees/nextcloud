# Nextcloud Docker Compose Setup

This Docker Compose configuration provides a complete Nextcloud setup with the following components:

- **Nextcloud**: The main application (using official Nextcloud image with auto-configuration)
- **PostgreSQL**: Database backend
- **Redis**: Memory cache for improved performance
- **Traefik**: Reverse proxy with automatic SSL certificates

## Features

- **ARM64 Compatible**: All images support ARM64 architecture
- **Automatic Configuration**: Complete setup via environment variables
- **Environment-based Configuration**: All settings managed via `.env` file
- **Persistent Storage**: Separate volumes for app, data, and database
- **Network Security**: Isolated networks for different components
- **Automatic SSL**: Traefik integration with Let's Encrypt
- **Built-in Cron**: Official image includes automated background tasks
- **Database Auto-Setup**: Automatic database configuration and admin user creation

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
- `TZ`: Timezone setting
- `DOMAIN_NAME`: Your domain name for Traefik routing

### Admin Configuration
- `NEXTCLOUD_ADMIN_USER`: Nextcloud admin username
- `NEXTCLOUD_ADMIN_PASSWORD`: Nextcloud admin password

### Storage Paths
- `CONFIG_BASE`: Base path for configuration files
- `DATA_BASE`: Base path for user data
- `MEDIA_BASE`: Base path for media files (optional)

### Database Configuration
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
- `${CONFIG_BASE}/nextcloud/app` - Nextcloud application and configuration
- `${DATA_BASE}/nextcloud/data` - User data files
- `${CONFIG_BASE}/nextcloud/db` - PostgreSQL database files
- `${CONFIG_BASE}/nextcloud/redis` - Redis cache data

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

### Database Connection
Check database connectivity:
```bash
docker-compose exec nextcloud_db psql -U nextcloud -d nextcloud
```

### Redis Connection
Verify Redis is working:
```bash
docker-compose exec nextcloud_redis redis-cli -a your_redis_password ping
```

## Initial Configuration

The official Nextcloud image handles all initial configuration automatically using environment variables:

1. **Automatic Database Setup:**
   - Database connection is configured automatically
   - Database, tables, and users are created automatically
   - Admin user is created with your specified credentials

2. **Automatic Redis Setup:**
   - Redis cache is configured automatically for improved performance
   - Session storage is handled by Redis

3. **Automatic Security Configuration:**
   - Trusted domains are set automatically
   - Reverse proxy headers are configured for Traefik
   - HTTPS protocol detection is properly configured

**No manual web-based setup required!** Simply start the containers and access your Nextcloud instance.

## Performance Optimization

For better performance, consider:
- Tuning PostgreSQL configuration (shared_buffers, effective_cache_size)
- Configuring Redis as session handler
- Using SSD storage for database and cache
- Monitoring resource usage
- Enabling PostgreSQL connection pooling if needed

## Support

For issues specific to this setup, check:
- Docker Compose logs
- Nextcloud admin documentation
- LinuxServer.io documentation for the Nextcloud image 