# APKRoja Deployment Guide

This guide provides detailed instructions for deploying the APKRoja APK download website to various hosting platforms.

## 🌐 Deployment Options

### Option 1: Single Server Deployment (Recommended)

Deploy both frontend and backend on a single server using Flask to serve everything.

#### Requirements
- VPS or dedicated server (minimum 1GB RAM)
- Ubuntu 20.04+ or similar Linux distribution
- Domain name with DNS access
- SSL certificate (Let's Encrypt recommended)

#### Step-by-Step Deployment

1. **Server Setup**
```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install required packages
sudo apt install python3 python3-pip python3-venv nginx git -y

# Install Node.js and pnpm
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs
npm install -g pnpm
```

2. **Clone and Setup Project**
```bash
# Clone repository
git clone <your-repo-url> /var/www/apkroja
cd /var/www/apkroja

# Setup backend
cd apkroja-backend
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt

# Setup frontend
cd ../apkroja-frontend
pnpm install
pnpm run build

# Copy built files to Flask static directory
cp -r dist/* ../apkroja-backend/src/static/
```

3. **Configure Environment**
```bash
# Create environment file
cd /var/www/apkroja/apkroja-backend
cat > .env << EOF
SECRET_KEY=$(python3 -c 'import secrets; print(secrets.token_hex(32))')
DATABASE_URL=sqlite:///apkroja.db
ADMIN_USERNAME=admin
ADMIN_PASSWORD=your_secure_password_here
FLASK_ENV=production
EOF
```

4. **Setup Systemd Service**
```bash
# Create service file
sudo cat > /etc/systemd/system/apkroja.service << EOF
[Unit]
Description=APKRoja Flask Application
After=network.target

[Service]
User=www-data
Group=www-data
WorkingDirectory=/var/www/apkroja/apkroja-backend
Environment=PATH=/var/www/apkroja/apkroja-backend/venv/bin
ExecStart=/var/www/apkroja/apkroja-backend/venv/bin/python src/main.py
Restart=always

[Install]
WantedBy=multi-user.target
EOF

# Set permissions
sudo chown -R www-data:www-data /var/www/apkroja
sudo chmod -R 755 /var/www/apkroja

# Enable and start service
sudo systemctl daemon-reload
sudo systemctl enable apkroja
sudo systemctl start apkroja
```

5. **Configure Nginx**
```bash
# Create Nginx configuration
sudo cat > /etc/nginx/sites-available/apkroja << EOF
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;
    
    location / {
        proxy_pass http://127.0.0.1:5000;
        proxy_set_header Host \$host;
        proxy_set_header X-Real-IP \$remote_addr;
        proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto \$scheme;
    }
    
    # Static files (optional, for better performance)
    location /static/ {
        alias /var/www/apkroja/apkroja-backend/src/static/;
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
}
EOF

# Enable site
sudo ln -s /etc/nginx/sites-available/apkroja /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

6. **Setup SSL with Let's Encrypt**
```bash
# Install Certbot
sudo apt install certbot python3-certbot-nginx -y

# Get SSL certificate
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com

# Auto-renewal
sudo crontab -e
# Add this line:
# 0 12 * * * /usr/bin/certbot renew --quiet
```

### Option 2: Separate Frontend/Backend Deployment

Deploy frontend and backend separately for better scalability.

#### Frontend Deployment (Netlify/Vercel)

1. **Build for Production**
```bash
cd apkroja-frontend
# Update API URL in src/services/api.js
# Change API_BASE_URL to your backend URL
pnpm run build
```

2. **Deploy to Netlify**
```bash
# Install Netlify CLI
npm install -g netlify-cli

# Deploy
netlify deploy --prod --dir=dist
```

3. **Deploy to Vercel**
```bash
# Install Vercel CLI
npm install -g vercel

# Deploy
vercel --prod
```

#### Backend Deployment (Heroku/DigitalOcean)

1. **Heroku Deployment**
```bash
# Create Procfile
echo "web: python src/main.py" > Procfile

# Create runtime.txt
echo "python-3.11.0" > runtime.txt

# Deploy
heroku create apkroja-api
heroku config:set SECRET_KEY=your_secret_key
heroku config:set DATABASE_URL=your_database_url
git push heroku main
```

2. **DigitalOcean App Platform**
```yaml
# app.yaml
name: apkroja-backend
services:
- name: api
  source_dir: /
  github:
    repo: your-username/apkroja
    branch: main
  run_command: python src/main.py
  environment_slug: python
  instance_count: 1
  instance_size_slug: basic-xxs
  envs:
  - key: SECRET_KEY
    value: your_secret_key
  - key: DATABASE_URL
    value: your_database_url
```

### Option 3: Docker Deployment

Containerized deployment for easy scaling and management.

1. **Create Dockerfile**
```dockerfile
# Frontend build stage
FROM node:20-alpine AS frontend-build
WORKDIR /app/frontend
COPY apkroja-frontend/package*.json ./
RUN npm install
COPY apkroja-frontend/ ./
RUN npm run build

# Backend stage
FROM python:3.11-slim
WORKDIR /app

# Install system dependencies
RUN apt-get update && apt-get install -y \
    gcc \
    && rm -rf /var/lib/apt/lists/*

# Copy backend files
COPY apkroja-backend/ ./
RUN pip install --no-cache-dir -r requirements.txt

# Copy built frontend
COPY --from=frontend-build /app/frontend/dist/ ./src/static/

# Create non-root user
RUN useradd --create-home --shell /bin/bash app
RUN chown -R app:app /app
USER app

EXPOSE 5000
CMD ["python", "src/main.py"]
```

2. **Docker Compose**
```yaml
version: '3.8'
services:
  apkroja:
    build: .
    ports:
      - "5000:5000"
    environment:
      - SECRET_KEY=your_secret_key
      - DATABASE_URL=sqlite:///apkroja.db
    volumes:
      - ./data:/app/data
    restart: unless-stopped
  
  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
      - ./ssl:/etc/nginx/ssl
    depends_on:
      - apkroja
    restart: unless-stopped
```

3. **Deploy with Docker**
```bash
# Build and run
docker-compose up -d

# View logs
docker-compose logs -f apkroja
```

## 🔧 Production Configuration

### Environment Variables

Create a comprehensive `.env` file:
```env
# Flask Configuration
SECRET_KEY=your-super-secret-key-here
FLASK_ENV=production
DEBUG=False

# Database
DATABASE_URL=sqlite:///apkroja.db
# For PostgreSQL: postgresql://user:password@localhost/apkroja

# Admin Credentials
ADMIN_USERNAME=admin
ADMIN_PASSWORD=your-secure-password

# Email Configuration (for contact forms)
MAIL_SERVER=smtp.gmail.com
MAIL_PORT=587
MAIL_USE_TLS=True
MAIL_USERNAME=your-email@gmail.com
MAIL_PASSWORD=your-app-password

# External Services
GOOGLE_ANALYTICS_ID=GA_MEASUREMENT_ID
ADSENSE_CLIENT_ID=ca-pub-xxxxxxxxxx

# Security
SESSION_COOKIE_SECURE=True
SESSION_COOKIE_HTTPONLY=True
PERMANENT_SESSION_LIFETIME=3600
```

### Database Migration

For production, consider using PostgreSQL:

1. **Install PostgreSQL**
```bash
sudo apt install postgresql postgresql-contrib -y
sudo -u postgres createuser --interactive
sudo -u postgres createdb apkroja
```

2. **Update Database URL**
```env
DATABASE_URL=postgresql://username:password@localhost/apkroja
```

3. **Install PostgreSQL adapter**
```bash
pip install psycopg2-binary
```

### Performance Optimization

1. **Enable Gzip Compression**
```nginx
# Add to Nginx configuration
gzip on;
gzip_vary on;
gzip_min_length 1024;
gzip_types text/plain text/css text/xml text/javascript application/javascript application/xml+rss application/json;
```

2. **Static File Caching**
```nginx
location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg)$ {
    expires 1y;
    add_header Cache-Control "public, immutable";
}
```

3. **Database Optimization**
```python
# Add database indexes in models
class App(db.Model):
    # ... existing fields ...
    
    # Add indexes for better performance
    __table_args__ = (
        db.Index('idx_app_slug', 'slug'),
        db.Index('idx_app_category', 'category_id'),
        db.Index('idx_app_featured', 'is_featured'),
    )
```

### Security Hardening

1. **Firewall Configuration**
```bash
# UFW firewall
sudo ufw allow ssh
sudo ufw allow 'Nginx Full'
sudo ufw enable
```

2. **Fail2Ban Setup**
```bash
sudo apt install fail2ban -y
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
```

3. **Regular Updates**
```bash
# Create update script
cat > /usr/local/bin/update-apkroja.sh << EOF
#!/bin/bash
cd /var/www/apkroja
git pull origin main
cd apkroja-frontend
pnpm install
pnpm run build
cp -r dist/* ../apkroja-backend/src/static/
sudo systemctl restart apkroja
EOF

chmod +x /usr/local/bin/update-apkroja.sh
```

## 📊 Monitoring and Maintenance

### Log Management

1. **Application Logs**
```python
# Add to Flask app
import logging
from logging.handlers import RotatingFileHandler

if not app.debug:
    file_handler = RotatingFileHandler('logs/apkroja.log', maxBytes=10240, backupCount=10)
    file_handler.setFormatter(logging.Formatter(
        '%(asctime)s %(levelname)s: %(message)s [in %(pathname)s:%(lineno)d]'
    ))
    file_handler.setLevel(logging.INFO)
    app.logger.addHandler(file_handler)
```

2. **Nginx Logs**
```bash
# Monitor access logs
sudo tail -f /var/log/nginx/access.log

# Monitor error logs
sudo tail -f /var/log/nginx/error.log
```

### Backup Strategy

1. **Database Backup**
```bash
# Create backup script
cat > /usr/local/bin/backup-apkroja.sh << EOF
#!/bin/bash
BACKUP_DIR="/var/backups/apkroja"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p $BACKUP_DIR

# Backup database
cp /var/www/apkroja/apkroja-backend/apkroja.db $BACKUP_DIR/apkroja_$DATE.db

# Backup uploaded files
tar -czf $BACKUP_DIR/uploads_$DATE.tar.gz /var/www/apkroja/uploads/

# Keep only last 30 days
find $BACKUP_DIR -name "*.db" -mtime +30 -delete
find $BACKUP_DIR -name "*.tar.gz" -mtime +30 -delete
EOF

chmod +x /usr/local/bin/backup-apkroja.sh

# Schedule daily backups
echo "0 2 * * * /usr/local/bin/backup-apkroja.sh" | sudo crontab -
```

### Health Monitoring

1. **System Monitoring**
```bash
# Install monitoring tools
sudo apt install htop iotop nethogs -y

# Monitor system resources
htop
```

2. **Application Monitoring**
```python
# Add health check endpoint
@app.route('/health')
def health_check():
    return {'status': 'healthy', 'timestamp': datetime.utcnow().isoformat()}
```

## 🚨 Troubleshooting

### Common Issues

1. **Service Won't Start**
```bash
# Check service status
sudo systemctl status apkroja

# Check logs
sudo journalctl -u apkroja -f

# Check file permissions
ls -la /var/www/apkroja/
```

2. **Database Connection Issues**
```bash
# Check database file permissions
ls -la /var/www/apkroja/apkroja-backend/apkroja.db

# Test database connection
cd /var/www/apkroja/apkroja-backend
source venv/bin/activate
python -c "from src.main import db; print('Database connection successful')"
```

3. **Nginx Configuration Issues**
```bash
# Test Nginx configuration
sudo nginx -t

# Check Nginx logs
sudo tail -f /var/log/nginx/error.log
```

### Performance Issues

1. **High Memory Usage**
```bash
# Monitor memory usage
free -h
ps aux | grep python

# Restart service if needed
sudo systemctl restart apkroja
```

2. **Slow Database Queries**
```python
# Enable query logging in Flask
app.config['SQLALCHEMY_ECHO'] = True
```

3. **High CPU Usage**
```bash
# Monitor CPU usage
top
htop

# Check for runaway processes
ps aux | sort -nrk 3,3 | head -5
```

## 📈 Scaling Considerations

### Horizontal Scaling

1. **Load Balancer Setup**
```nginx
upstream apkroja_backend {
    server 127.0.0.1:5000;
    server 127.0.0.1:5001;
    server 127.0.0.1:5002;
}

server {
    location / {
        proxy_pass http://apkroja_backend;
    }
}
```

2. **Database Scaling**
- Use PostgreSQL with read replicas
- Implement database connection pooling
- Consider Redis for caching

3. **CDN Integration**
```nginx
# Serve static files from CDN
location /static/ {
    return 301 https://cdn.yourdomain.com$request_uri;
}
```

### Vertical Scaling

1. **Increase Server Resources**
- Upgrade RAM and CPU
- Use SSD storage
- Optimize database configuration

2. **Application Optimization**
- Implement caching layers
- Optimize database queries
- Use async processing for heavy tasks

This deployment guide provides comprehensive instructions for deploying APKRoja in various environments. Choose the option that best fits your infrastructure and scaling requirements.

