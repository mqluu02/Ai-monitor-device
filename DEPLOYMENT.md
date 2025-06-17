# AI CNC Monitor Deployment Guide

This guide covers deploying AI CNC Monitor in production environments for real CNC machine shops.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Production Architecture](#production-architecture)
- [Deployment Options](#deployment-options)
- [Environment Configuration](#environment-configuration)
- [Database Setup](#database-setup)
- [Video Streaming Setup](#video-streaming-setup)
- [Security Configuration](#security-configuration)
- [Monitoring & Maintenance](#monitoring--maintenance)
- [Troubleshooting](#troubleshooting)

## Prerequisites

### System Requirements

**Minimum Requirements:**
- CPU: 4 cores (2.4GHz+)
- RAM: 8GB
- Storage: 100GB SSD
- Network: 1Gbps ethernet
- OS: Ubuntu 20.04+, CentOS 8+, or Windows Server 2019+

**Recommended for Production:**
- CPU: 8 cores (3.0GHz+)
- RAM: 16GB
- Storage: 500GB SSD + 2TB HDD for video storage
- Network: Dual 1Gbps ethernet (redundancy)
- OS: Ubuntu 22.04 LTS

### Software Dependencies

- **Node.js**: 18.18.0 or higher
- **PNPM**: 9.12.0 or higher
- **Docker**: 24.0+ (optional but recommended)
- **Nginx**: For reverse proxy and SSL termination
- **PostgreSQL**: 15+ (via Supabase or self-hosted)

## Production Architecture

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   CNC Machines  │    │    Cameras      │    │    Sensors      │
│                 │    │                 │    │                 │
└─────────┬───────┘    └─────────┬───────┘    └─────────┬───────┘
          │                      │                      │
          └──────────────────────┼──────────────────────┘
                                 │
                    ┌─────────────▼──────────────┐
                    │     Network Switch         │
                    └─────────────┬──────────────┘
                                  │
                    ┌─────────────▼──────────────┐
                    │     AI CNC Monitor         │
                    │                            │
                    │  ┌──────────┐              │
                    │  │   Web    │              │
                    │  │   App    │              │
                    │  └──────────┘              │
                    │  ┌──────────┐              │
                    │  │ Database │              │
                    │  └──────────┘              │
                    │  ┌──────────┐              │
                    │  │  Video   │              │
                    │  │ Storage  │              │
                    │  └──────────┘              │
                    └────────────────────────────┘
```

## Deployment Options

### Option 1: Docker Deployment (Recommended)

#### 1.1 Create Docker Compose Configuration

```yaml
# docker-compose.production.yml
version: '3.8'

services:
  app:
    build:
      context: .
      dockerfile: Dockerfile.production
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - NEXT_PUBLIC_SUPABASE_URL=${SUPABASE_URL}
      - NEXT_PUBLIC_SUPABASE_ANON_KEY=${SUPABASE_ANON_KEY}
      - SUPABASE_SERVICE_ROLE_KEY=${SUPABASE_SERVICE_ROLE_KEY}
    volumes:
      - video_storage:/app/storage/videos
      - app_logs:/app/logs
    restart: unless-stopped
    depends_on:
      - nginx

  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
      - ./ssl:/etc/nginx/ssl
      - video_storage:/var/www/videos:ro
    restart: unless-stopped

  redis:
    image: redis:alpine
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data
    restart: unless-stopped

volumes:
  video_storage:
  app_logs:
  redis_data:
```

#### 1.2 Create Production Dockerfile

```dockerfile
# Dockerfile.production
FROM node:18-alpine AS deps
WORKDIR /app
COPY package.json pnpm-lock.yaml ./
RUN npm install -g pnpm
RUN pnpm install --frozen-lockfile --production

FROM node:18-alpine AS builder
WORKDIR /app
COPY . .
COPY --from=deps /app/node_modules ./node_modules
RUN npm install -g pnpm
RUN pnpm build

FROM node:18-alpine AS runner
WORKDIR /app
ENV NODE_ENV production

RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs

COPY --from=builder /app/apps/web/.next/standalone ./
COPY --from=builder --chown=nextjs:nodejs /app/apps/web/.next/static ./apps/web/.next/static
COPY --from=builder /app/apps/web/public ./apps/web/public

USER nextjs

EXPOSE 3000
ENV PORT 3000

CMD ["node", "apps/web/server.js"]
```

#### 1.3 Deploy with Docker

```bash
# Build and start services
docker-compose -f docker-compose.production.yml up -d

# View logs
docker-compose -f docker-compose.production.yml logs -f

# Update deployment
docker-compose -f docker-compose.production.yml pull
docker-compose -f docker-compose.production.yml up -d
```

### Option 2: Manual Deployment

#### 2.1 Server Preparation

```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install Node.js 18
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs

# Install PNPM
curl -fsSL https://get.pnpm.io/install.sh | sh -

# Install PM2 for process management
npm install -g pm2

# Create application user
sudo useradd -m -s /bin/bash cncmonitor
sudo usermod -aG sudo cncmonitor
```

#### 2.2 Application Deployment

```bash
# Switch to application user
sudo su - cncmonitor

# Clone repository
git clone https://github.com/yourusername/ai-cnc-monitor.git
cd ai-cnc-monitor

# Install dependencies
pnpm install

# Build application
pnpm build

# Create PM2 ecosystem file
cat > ecosystem.config.js << EOF
module.exports = {
  apps: [{
    name: 'cnc-monitor',
    script: 'apps/web/server.js',
    cwd: '/home/cncmonitor/ai-cnc-monitor',
    instances: 'max',
    exec_mode: 'cluster',
    env: {
      NODE_ENV: 'production',
      PORT: 3000
    }
  }]
}
EOF

# Start application
pm2 start ecosystem.config.js
pm2 save
pm2 startup
```

### Option 3: Cloud Deployment (Vercel)

#### 3.1 Vercel Configuration

```bash
# Install Vercel CLI
npm install -g vercel

# Deploy to Vercel
vercel --prod

# Configure environment variables in Vercel dashboard
```

```json
// vercel.json
{
  "version": 2,
  "builds": [
    {
      "src": "apps/web/package.json",
      "use": "@vercel/next"
    }
  ],
  "routes": [
    {
      "src": "/(.*)",
      "dest": "/apps/web/$1"
    }
  ],
  "env": {
    "NEXT_PUBLIC_SUPABASE_URL": "@supabase-url",
    "NEXT_PUBLIC_SUPABASE_ANON_KEY": "@supabase-anon-key",
    "SUPABASE_SERVICE_ROLE_KEY": "@supabase-service-key"
  }
}
```

## Environment Configuration

### Production Environment Variables

Create a `.env.production` file:

```bash
# Database
NEXT_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_anon_key
SUPABASE_SERVICE_ROLE_KEY=your_service_role_key

# Application
NODE_ENV=production
PORT=3000
HOSTNAME=0.0.0.0

# Video Storage
VIDEO_STORAGE_PATH=/app/storage/videos
MAX_VIDEO_SIZE=500MB
VIDEO_RETENTION_DAYS=30

# Monitoring
LOG_LEVEL=warn
ENABLE_METRICS=true
METRICS_PORT=9090

# Security
SESSION_SECRET=your_session_secret_here
CSRF_SECRET=your_csrf_secret_here
JWT_SECRET=your_jwt_secret_here

# SMTP for notifications (optional)
SMTP_HOST=smtp.your-provider.com
SMTP_PORT=587
SMTP_USER=your_email@domain.com
SMTP_PASS=your_email_password

# External integrations
CAMERA_DEFAULT_USERNAME=admin
CAMERA_DEFAULT_PASSWORD=your_camera_password
```

## Database Setup

### Option 1: Supabase Cloud (Recommended)

1. **Create Supabase Project**
   ```bash
   # Visit https://supabase.com
   # Create new project
   # Copy connection details
   ```

2. **Run Migrations**
   ```bash
   pnpm supabase:web:typegen
   ```

### Option 2: Self-hosted PostgreSQL

1. **Install PostgreSQL**
   ```bash
   sudo apt install postgresql postgresql-contrib
   sudo systemctl start postgresql
   sudo systemctl enable postgresql
   ```

2. **Create Database**
   ```sql
   CREATE DATABASE cnc_monitor;
   CREATE USER cnc_user WITH PASSWORD 'secure_password';
   GRANT ALL PRIVILEGES ON DATABASE cnc_monitor TO cnc_user;
   ```

3. **Configure Connection**
   ```bash
   DATABASE_URL=postgresql://cnc_user:secure_password@localhost:5432/cnc_monitor
   ```

## Video Streaming Setup

### Camera Configuration

#### IP Cameras
```bash
# Test RTSP stream
ffmpeg -i rtsp://camera_ip:554/stream -t 10 -f null -

# Configure in application
CAMERA_STREAMS='[
  {
    "id": "mill_001",
    "name": "CNC Mill #1",
    "url": "rtsp://192.168.1.100:554/stream",
    "type": "rtsp"
  }
]'
```

#### USB Cameras
```bash
# List available cameras
v4l2-ctl --list-devices

# Test camera
ffmpeg -f v4l2 -i /dev/video0 -t 10 test.mp4
```

### Video Storage Configuration

```bash
# Create storage directory
sudo mkdir -p /var/cnc-monitor/videos
sudo chown cncmonitor:cncmonitor /var/cnc-monitor/videos

# Configure log rotation for videos
cat > /etc/logrotate.d/cnc-videos << EOF
/var/cnc-monitor/videos/*.mp4 {
    daily
    rotate 30
    compress
    delaycompress
    missingok
    notifempty
    copytruncate
}
EOF
```

## Security Configuration

### SSL/TLS Setup

#### Using Let's Encrypt

```bash
# Install Certbot
sudo apt install certbot python3-certbot-nginx

# Generate certificate
sudo certbot --nginx -d your-domain.com

# Auto-renewal
sudo crontab -e
# Add: 0 12 * * * /usr/bin/certbot renew --quiet
```

#### Nginx Configuration

```nginx
# /etc/nginx/sites-available/cnc-monitor
server {
    listen 80;
    server_name your-domain.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name your-domain.com;

    ssl_certificate /etc/letsencrypt/live/your-domain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/your-domain.com/privkey.pem;
    
    # Security headers
    add_header X-Frame-Options DENY;
    add_header X-Content-Type-Options nosniff;
    add_header X-XSS-Protection "1; mode=block";
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains";

    # Application proxy
    location / {
        proxy_pass http://127.0.0.1:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
    }

    # Video streaming
    location /videos {
        alias /var/cnc-monitor/videos;
        add_header Cache-Control "no-store, no-cache, must-revalidate";
        expires off;
    }
}
```

### Firewall Configuration

```bash
# UFW firewall setup
sudo ufw enable
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Allow essential services
sudo ufw allow ssh
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

# Allow CNC network (adjust range as needed)
sudo ufw allow from 192.168.1.0/24

# Deny all other traffic
sudo ufw --force enable
```

## Monitoring & Maintenance

### Application Monitoring

```bash
# PM2 monitoring
pm2 monit

# System monitoring
sudo apt install htop iotop nethogs

# Log monitoring
tail -f /var/log/nginx/access.log
pm2 logs cnc-monitor
```

### Health Checks

Create a health check script:

```bash
#!/bin/bash
# /home/cncmonitor/health-check.sh

# Check application health
if ! curl -f http://localhost:3000/api/health > /dev/null 2>&1; then
    echo "Application health check failed"
    pm2 restart cnc-monitor
fi

# Check disk space
USAGE=$(df /var/cnc-monitor | awk 'NR==2 {print $5}' | sed 's/%//')
if [ $USAGE -gt 90 ]; then
    echo "Disk usage high: ${USAGE}%"
    # Clean old videos
    find /var/cnc-monitor/videos -name "*.mp4" -mtime +7 -delete
fi

# Check camera connectivity
# Add your camera IP addresses
CAMERAS=("192.168.1.100" "192.168.1.101")
for camera in "${CAMERAS[@]}"; do
    if ! ping -c 1 $camera > /dev/null 2>&1; then
        echo "Camera $camera unreachable"
    fi
done
```

### Automated Backups

```bash
#!/bin/bash
# /home/cncmonitor/backup.sh

BACKUP_DIR="/var/backups/cnc-monitor"
DATE=$(date +%Y%m%d_%H%M%S)

# Create backup directory
mkdir -p $BACKUP_DIR

# Backup database
pg_dump cnc_monitor > $BACKUP_DIR/database_$DATE.sql

# Backup configuration
tar -czf $BACKUP_DIR/config_$DATE.tar.gz /home/cncmonitor/ai-cnc-monitor/.env.production

# Backup recent videos (last 7 days)
find /var/cnc-monitor/videos -name "*.mp4" -mtime -7 | tar -czf $BACKUP_DIR/videos_$DATE.tar.gz -T -

# Clean old backups (keep 30 days)
find $BACKUP_DIR -name "*.sql" -mtime +30 -delete
find $BACKUP_DIR -name "*.tar.gz" -mtime +30 -delete

# Schedule in crontab
# 0 2 * * * /home/cncmonitor/backup.sh
```

## Troubleshooting

### Common Issues

#### Application Won't Start
```bash
# Check logs
pm2 logs cnc-monitor

# Check environment variables
pm2 env 0

# Restart application
pm2 restart cnc-monitor
```

#### Video Streams Not Working
```bash
# Test camera connectivity
ping camera_ip

# Check RTSP stream
ffplay rtsp://camera_ip:554/stream

# Verify network settings
sudo netstat -tlnp | grep :554
```

#### High CPU/Memory Usage
```bash
# Monitor resources
htop

# Check video processing
ps aux | grep ffmpeg

# Adjust video quality settings
# Reduce frame rate or resolution
```

#### Database Connection Issues
```bash
# Test connection
psql -h hostname -U username -d database_name

# Check Supabase status
curl https://status.supabase.com/api/v2/status.json
```

### Performance Optimization

```bash
# Enable Nginx gzip compression
gzip on;
gzip_vary on;
gzip_types text/plain text/css application/json application/javascript text/xml application/xml;

# Optimize video encoding
ffmpeg -i input.rtsp -c:v h264_nvenc -preset fast -crf 23 output.mp4

# Database optimization
# Add indexes for frequently queried columns
CREATE INDEX idx_logs_timestamp ON logs(timestamp);
CREATE INDEX idx_alerts_machine_id ON alerts(machine_id);
```

## Support

For deployment issues:
- Check the [troubleshooting section](#troubleshooting)
- Review logs carefully
- Ensure all prerequisites are met
- Contact support with detailed error messages

Remember to always test deployments in a staging environment first! 