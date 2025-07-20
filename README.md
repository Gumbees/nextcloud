# Nextcloud Docker Compose Setup - ARM Optimized for 1TB Files

This Docker Compose configuration provides a complete Nextcloud setup optimized for ARM architecture with support for 1TB file uploads. The setup includes:

- **Nextcloud**: ARM-optimized application with large file support (using official Nextcloud image)
- **PostgreSQL**: ARM-tuned database backend with parallel processing
- **Redis**: ARM-optimized memory cache with multithreading support
- **Traefik**: Reverse proxy with automatic SSL certificates and large file buffering

## Features

- **ARM64 Optimized**: All images support ARM64 architecture with specific performance tuning
- **1TB File Support**: Complete configuration for handling files up to 1 terabyte
- **Multithreading**: Redis configured with I/O threads for ARM processors
- **Resource Management**: Configurable CPU and memory limits for each service
- **Automatic Configuration**: Complete setup via environment variables
- **Environment-based Configuration**: All settings managed via `.env` file
- **Persistent Storage**: Separate volumes for app, data, and database with optimized mount options
- **Network Security**: Isolated networks for different components
- **Automatic SSL**: Traefik integration with Let's Encrypt
- **Built-in Cron**: Official image includes automated background tasks
- **Database Auto-Setup**: Automatic database configuration and admin user creation

## ARM Performance Optimizations

### Nextcloud Application
- ARM64 platform specification
- PHP OPcache tuned for ARM processors
- **PHP-FPM process management** optimized for concurrent requests
- **Apache MPM configuration** for ARM multithreading
- Large file handling with 1TB upload limits
- Resource limits and reservations
- Optimized file descriptor limits

### PostgreSQL Database
- ARM-specific parallel worker configuration
- WAL compression and optimized checkpoint settings
- Tuned shared buffers and cache sizes
- **Increased connection limits** for concurrent PHP processes
- **Connection timeout optimization** to prevent hangs
- Parallel maintenance operations
- I/O concurrency optimization

### Redis Cache
- I/O threading enabled for ARM CPUs
- Lazy free operations for better performance
- Optimized TCP settings
- Memory-efficient persistence configuration

## Quick Start

1. **Copy the environment file:**
   ```bash
   cp env.example .env
   ```

2. **Edit the `.env` file for your ARM device:**
   - Set your domain name
   - Configure volume paths
   - Set secure passwords
   - **Adjust resource limits based on your ARM device** (see examples in env.example)
   - Configure for 1TB file support (already optimized)

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
   - Complete the initial setup wizard (if not auto-configured)

## Environment Variables

### ARM Resource Configuration
- `NEXTCLOUD_CPU_LIMIT` / `NEXTCLOUD_MEMORY_LIMIT`: Nextcloud container limits
- `DB_CPU_LIMIT` / `DB_MEMORY_LIMIT`: PostgreSQL container limits  
- `REDIS_CPU_LIMIT` / `REDIS_MEMORY_LIMIT`: Redis container limits
- `*_CPU_RESERVATION` / `*_MEMORY_RESERVATION`: Guaranteed resource reservations

### Large File Support (1TB)
- `PHP_MEMORY_LIMIT`: PHP memory allocation (default: 2G)
- `PHP_UPLOAD_LIMIT`: Maximum upload size (default: 1024G)
- `PHP_MAX_EXECUTION_TIME`: Script execution time (default: 3600s)
- `PHP_POST_MAX_SIZE`: Maximum POST data size (default: 1024G)
- `APACHE_BODY_LIMIT`: Apache request body limit

### PHP OPcache ARM Optimization
- `PHP_OPCACHE_MEMORY_CONSUMPTION`: OPcache memory (default: 256MB)
- `PHP_OPCACHE_MAX_ACCELERATED_FILES`: Cached file limit (default: 10000)
- `PHP_OPCACHE_REVALIDATE_FREQ`: Cache revalidation frequency

### PHP-FPM Concurrency Management
- `PHP_FPM_PM_MAX_CHILDREN`: Maximum child processes (default: 40)
- `PHP_FPM_PM_START_SERVERS`: Starting child processes (default: 8)
- `PHP_FPM_PM_MIN_SPARE_SERVERS`: Minimum idle processes (default: 4)
- `PHP_FPM_PM_MAX_SPARE_SERVERS`: Maximum idle processes (default: 12)
- `PHP_FPM_PM_MAX_REQUESTS`: Requests before restart (default: 1000)
- `PHP_FPM_REQUEST_TERMINATE_TIMEOUT`: Max request time (default: 3600s)

### Apache Concurrent Connection Handling
- `APACHE_MAX_REQUEST_WORKERS`: Max simultaneous requests (default: 400)
- `APACHE_THREADS_PER_CHILD`: Threads per process (default: 25)
- `APACHE_SERVER_LIMIT`: Maximum server processes (default: 16)

### PostgreSQL ARM Tuning
- `DB_MAX_WORKER_PROCESSES`: Background worker processes (default: 8)
- `DB_MAX_PARALLEL_WORKERS`: Parallel workers (default: 8)
- `DB_MAX_PARALLEL_WORKERS_PER_GATHER`: Per-query parallel workers (default: 4)
- `DB_EFFECTIVE_IO_CONCURRENCY`: I/O concurrency setting (default: 200)
- `DB_SHARED_BUFFERS`: Database shared memory (default: 512MB)
- `DB_EFFECTIVE_CACHE_SIZE`: Expected cache size (default: 1536MB)

### Redis ARM Multithreading
- `REDIS_IO_THREADS`: I/O thread count (default: 4)
- `REDIS_IO_THREADS_DO_READS`: Enable threaded reads (default: yes)
- `REDIS_MAXMEMORY`: Memory limit (default: 768mb)
- `REDIS_MAXMEMORY_POLICY`: Eviction policy (default: allkeys-lru)

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

## ARM Device Recommendations

### Raspberry Pi 4 (4GB RAM)
```bash
NEXTCLOUD_CPU_LIMIT=3.0
NEXTCLOUD_MEMORY_LIMIT=2G
DB_CPU_LIMIT=1.0
DB_MEMORY_LIMIT=1G
REDIS_CPU_LIMIT=0.5
REDIS_MEMORY_LIMIT=512M
```

### Apple Silicon Mac Mini (16GB RAM)
```bash
NEXTCLOUD_CPU_LIMIT=6.0
NEXTCLOUD_MEMORY_LIMIT=8G
DB_CPU_LIMIT=4.0
DB_MEMORY_LIMIT=4G
REDIS_CPU_LIMIT=2.0
REDIS_MEMORY_LIMIT=2G
```

### AWS Graviton Instance (c6g.2xlarge)
```bash
NEXTCLOUD_CPU_LIMIT=8.0
NEXTCLOUD_MEMORY_LIMIT=12G
DB_CPU_LIMIT=4.0
DB_MEMORY_LIMIT=6G
REDIS_CPU_LIMIT=2.0
REDIS_MEMORY_LIMIT=2G
```

## Volume Configuration

The setup supports flexible volume configurations optimized for large files:

### Local Volumes (Default - Optimized)
```bash
CONFIG_VOLUME_TYPE=none
CONFIG_VOLUME_OPTIONS=bind,rw,noatime
DATA_VOLUME_TYPE=none  
DATA_VOLUME_OPTIONS=bind,rw,noatime,async
```

### NFS Storage (Recommended for 1TB files)
```bash
CONFIG_VOLUME_TYPE=nfs
CONFIG_VOLUME_OPTIONS=addr=192.168.1.100,rw,nfsvers=4,hard,intr
DATA_VOLUME_TYPE=nfs
DATA_VOLUME_OPTIONS=addr=192.168.1.100,rw,nfsvers=4,hard,intr,async
```

### High-Performance SSD
```bash
CONFIG_VOLUME_OPTIONS=bind,rw,noatime,nodev,nosuid
DATA_VOLUME_OPTIONS=bind,rw,noatime,nodev,nosuid,async
```

## Network Architecture

- **nextcloud_apps**: Internal network for service communication
- **nextcloud_database_network**: Isolated database network
- **container_internet**: External network for internet access
- **MTU Optimization**: Set to 1500 for optimal ARM network performance

## Security Features

- Database isolated on separate network
- Redis password protection
- Traefik automatic SSL certificates with 1TB request buffering
- ARM-specific security optimizations
- Resource limits prevent resource exhaustion attacks

## 1TB File Upload Considerations

### Network Requirements
- Stable internet connection for large uploads
- Consider upload time: 1TB @ 100Mbps = ~22 hours
- Traefik configured with retry logic for network errors

### Storage Requirements
- Ensure sufficient disk space (1TB + overhead)
- Use fast storage (SSD recommended) for best performance
- Consider file system limits (ext4, XFS, etc.)

### Client Considerations
- Modern browsers support resumable uploads
- Consider using Nextcloud desktop client for large files
- Monitor upload progress and network stability

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

### Monitoring Resource Usage
```bash
# Monitor container resources
docker stats

# Check ARM CPU usage
docker-compose exec nextcloud htop

# Monitor database performance
docker-compose logs nextcloud_db | grep -E "(slow|performance)"
```

### Logs
```bash
docker-compose logs -f nextcloud
docker-compose logs -f nextcloud_db
docker-compose logs -f nextcloud_redis
```

## Troubleshooting

### PHP Timeout Issues with Concurrent Requests
```bash
# Check PHP-FPM process status
docker-compose exec nextcloud ps aux | grep php-fpm

# Monitor PHP-FPM pool status
docker-compose exec nextcloud cat /proc/*/stat | grep php-fpm

# Check current PHP-FPM configuration
docker-compose exec nextcloud php-fpm -tt

# Monitor Apache worker processes
docker-compose exec nextcloud apache2ctl status

# Check for memory issues
docker-compose exec nextcloud free -h
docker stats nextcloud_nextcloud
```

### Large File Upload Issues
```bash
# Check PHP limits
docker-compose exec nextcloud php -i | grep -E "(upload_max_filesize|post_max_size|memory_limit)"

# Monitor upload progress
docker-compose logs -f nextcloud | grep -i upload

# Check Traefik buffering
curl -I https://yourdomain.com
```

### ARM Performance Issues
```bash
# Check CPU architecture
docker-compose exec nextcloud uname -m

# Monitor ARM-specific metrics
docker-compose exec nextcloud_db pg_stat_activity

# Redis multithreading status
docker-compose exec nextcloud_redis redis-cli info threads
```

### Database Connection
Check database connectivity:
```bash
docker-compose exec nextcloud_db psql -U nextcloud -d nextcloud
```

### Redis Connection
Verify Redis multithreading:
```bash
docker-compose exec nextcloud_redis redis-cli -a your_redis_password info threads
docker-compose exec nextcloud_redis redis-cli -a your_redis_password ping
```

### HTTPS Warning Behind Reverse Proxy
If you see "Accessing site insecurely via HTTP" warning when using HTTPS through Traefik:
- The configuration includes automatic HTTPS detection for Traefik
- `OVERWRITEPROTOCOL=https` tells Nextcloud the external connection is HTTPS
- `TRUSTED_PROXIES=172.16.0.0/12` allows Nextcloud to trust Traefik's headers
- Large file buffering is configured in Traefik middleware
- Restart containers after configuration changes: `docker-compose restart nextcloud`

## Initial Configuration

The official Nextcloud image handles all initial configuration automatically using environment variables:

1. **Automatic Database Setup:**
   - Database connection is configured automatically
   - Database, tables, and users are created automatically
   - Admin user is created with your specified credentials

2. **Automatic Redis Setup:**
   - Redis cache is configured automatically for improved performance
   - Session storage is handled by Redis with ARM optimizations

3. **Automatic Security Configuration:**
   - Trusted domains are set automatically
   - Reverse proxy headers are configured for Traefik
   - HTTPS protocol detection is properly configured
   - Large file upload limits are set automatically

**No manual web-based setup required!** Simply start the containers and access your Nextcloud instance.

## Performance Optimization Summary

This setup provides the following ARM and large file optimizations:

### ARM-Specific Optimizations
- ✅ Platform targeting for ARM64
- ✅ CPU and memory resource limits
- ✅ PostgreSQL parallel processing tuned for ARM
- ✅ Redis I/O multithreading
- ✅ PHP OPcache ARM optimization
- ✅ Network MTU optimization

### 1TB File Support
- ✅ PHP upload limits set to 1TB
- ✅ Apache/Nginx body limits configured
- ✅ Traefik request buffering up to 1TB
- ✅ Database tuned for large file metadata
- ✅ Async volume mounts for performance
- ✅ Extended timeout configurations

### Additional Considerations
- Monitor disk I/O for large file operations
- Consider implementing file chunking for uploads > 100GB
- Use external object storage (S3, etc.) for files > 1TB if needed
- Regular maintenance and monitoring of system resources

## Support

For issues specific to this ARM-optimized setup:
- Check Docker Compose logs for ARM-specific errors
- Monitor resource usage during large file uploads
- Verify ARM64 platform compatibility
- Review PostgreSQL and Redis ARM performance metrics
- Consult Nextcloud admin documentation for large file best practices 