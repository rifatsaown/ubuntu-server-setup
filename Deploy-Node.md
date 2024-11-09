# Deploy Node.js Application on Ubuntu Server Using Nginx

## 1. Install Node.js

```bash
# First, update system packages
sudo apt update && sudo apt upgrade -y

# Install build essentials
sudo apt install -y build-essential

# Install NVM (Node Version Manager)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
source ~/.bashrc  # Reload shell configuration

# Install LTS Node.js (recommended for production)
nvm install --lts
nvm use --lts
nvm alias default 'lts/*'  # Set LTS as default
```

## 2. Install Yarn

```bash
npm install -g yarn
```

## 3. Install PM2(Production Process Manager for Node.js)

```bash
npm install -g pm2
```

## 4. Clone Node.js Application

```bash
git clone [repository_url] # Clone Node.js Application
cd [repository_name] # Change Directory
yarn install # Install Dependencies
```

## 5. Build Node.js Application

```bash
yarn build # Build Node.js Application
```

## 6. Start Node.js Application

```bash
# First, create ecosystem config
pm2 ecosystem init

# Edit ecosystem.config.js to include:
module.exports = {
  apps: [{
    name: "app_name",
    script: "npm",
    args: "start",
    env: {NODE_ENV: "production"},
    max_memory_restart: "1G",
    error_file: "/var/log/pm2/error.log",
    out_file: "/var/log/pm2/out.log",
    time: true
  }]
}

# Start using ecosystem file
pm2 start ecosystem.config.js

# Save PM2 process list and set up startup script
pm2 save
pm2 startup
```

## 7. Setup Nginx

```bash
sudo apt install -y nginx

# Create separate site configuration
sudo nano /etc/nginx/sites-available/myapp.conf
```

```nginx
server {
    listen 80;
    listen [::]:80;
    server_name example.com www.example.com;

    # Security headers
    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header X-Content-Type-Options "nosniff";

    # Gzip compression
    gzip on;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

    location / {
        proxy_pass http://localhost:3000;  # Specify your app port
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # Timeouts
        proxy_connect_timeout 60s;
        proxy_send_timeout 60s;
        proxy_read_timeout 60s;
    }
}
```

```bash
# Enable site and remove default
sudo ln -s /etc/nginx/sites-available/myapp.conf /etc/nginx/sites-enabled/
sudo rm /etc/nginx/sites-enabled/default

# Test nginx configuration
sudo nginx -t

# If test passes, restart nginx
sudo systemctl restart nginx
```

## 8. Setup Firewall

```bash
sudo ufw allow 'Nginx Full' # Allow Nginx Full
sudo ufw allow 'OpenSSH' # Allow OpenSSH
sudo ufw enable # Enable Firewall
sudo ufw status # Check Firewall Status
```

## 9. PM2 Commands

```bash
pm2 monit # Monitor Node.js Application

pm2 stop [app_name] # Stop Node.js Application

pm2 restart [app_name] # Restart Node.js Application

pm2 kill # Kill PM2

pm2 delete [app_name] # Delete Node.js Application
```

## 10. Setup Domain

```bash
sudo nano /etc/hosts # Open Hosts File
```

```plaintext
[server_ip] [domain_name] # Add Domain to Hosts File
```

## 10. Setup SSL (Certbot)

```bash
sudo apt install -y certbot python3-certbot-nginx # Install Certbot
sudo certbot --nginx # Setup SSL
```

```bash
sudo certbot renew # Renew SSL

sudo certbot renew --dry-run # Test SSL Renewal

sudo certbot certificates # Check SSL Certificates

sudo certbot delete --cert-name [domain_name] # Delete SSL Certificate
```

## 12. Setup Logrotate

```bash
# Create PM2 log directory
sudo mkdir -p /var/log/pm2

# Set proper permissions
sudo chown -R $USER:$USER /var/log/pm2

sudo nano /etc/logrotate.d/pm2
```

```plaintext
/var/log/pm2/*.log {
    daily
    rotate 7
    compress
    delaycompress
    missingok
    notifempty
    create 0640 $USER $USER
    sharedscripts
    postrotate
        pm2 reloadLogs
    endscript
}
```

## 13. Setup Backup

```bash
# Create backup directory
sudo mkdir -p /var/backups/node-app

# Create backup script
sudo nano /usr/local/bin/backup-node-app.sh
```

```bash
#!/bin/bash
TIMESTAMP=$(date +%Y%m%d_%H%M%S)
BACKUP_DIR="/var/backups/node-app"
APP_DIR="/path/to/your/app"  # Change this
PM2_DIR="/home/$USER/.pm2"

# Backup application files
tar -czf $BACKUP_DIR/app_$TIMESTAMP.tar.gz $APP_DIR

# Backup PM2 configuration
tar -czf $BACKUP_DIR/pm2_$TIMESTAMP.tar.gz $PM2_DIR

# Keep only last 7 days of backups
find $BACKUP_DIR -type f -mtime +7 -delete
```

```bash
# Make script executable
sudo chmod +x /usr/local/bin/backup-node-app.sh

# Set up daily cron job
sudo crontab -e
```

```plaintext
0 0 * * * /usr/local/bin/backup-node-app.sh
```

## 14. Monitoring and Security

```bash
# Install and configure fail2ban
sudo apt install -y fail2ban
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
sudo systemctl enable fail2ban
sudo systemctl start fail2ban

# Set up basic monitoring
pm2 install pm2-logrotate
pm2 set pm2-logrotate:max_size 10M
pm2 set pm2-logrotate:retain 7
```
