# Setup Nginx Server on Linux(Ubuntu) for First Time

## 1. Install Nginx

```bash
sudo apt install -y nginx # Install Nginx
sudo systemctl start nginx # Start Nginx
sudo systemctl enable nginx # Enable Nginx to Start on Boot
```

## 2. Configure Nginx

```bash
sudo nano /etc/nginx/sites-available/default # Open Nginx Configuration File
```

```nginx
server {
    listen 80 default_server; # Listen on Port 80
    listen [::]:80 default_server; # Listen on Port 80

    root /var/www/html; # Root Directory
    index index.html index.htm index.nginx-debian.html; # Index Files

    server_name _; # Server Name

    location / {
        try_files $uri $uri/ =404; # Redirect to 404 Page
    } # Location Block for Root Directory
}
```

## 3. Test Nginx Configuration

```bash
sudo nginx -t # Test Nginx Configuration
```

## 4. Reload Nginx

```bash
sudo systemctl reload nginx # Reload Nginx
```

## 5. Open Firewall for Nginx

```bash
sudo ufw allow 'Nginx HTTP' # Open Firewall for Nginx HTTP Port 80
sudo ufw enable
sudo ufw status
sudo ufw allow ssh (Port 22)
sudo ufw allow http (Port 80)
sudo ufw allow https (Port 443)
```

## 6. Check Nginx Status

```bash
sudo systemctl status nginx # Check Nginx Status
```

## 7. Access Nginx from Browser

```bash
# Open Browser and Enter Server IP Address
```

## 8. Nginx Configuration File

```bash
sudo nano /etc/nginx/nginx.conf # Open Nginx Configuration File
```

## 9. Nginx Log Files

```bash
sudo nano /var/log/nginx/access.log # Open Nginx Access Log File
sudo nano /var/log/nginx/error.log # Open Nginx Error Log File
```

## 10. Nginx Server Block Configuration

```bash
sudo nano /etc/nginx/sites-available/example.com # Create Nginx Server Block
sudo ln -s /etc/nginx/sites-available/example.com /etc/nginx/sites-enabled/ # Enable Nginx Server Block
sudo systemctl reload nginx # Reload Nginx
```

## 11. Nginx Reverse Proxy for JavaScript Applications

```bash
sudo nano /etc/nginx/sites-available/example.com
```

```nginx
server {
    listen 80;
    server_name example.com www.example.com;

    # React/Vue/Angular Static File Serving
    location / {
        root /var/www/html/my-app/build;  # Path to your built frontend files
        try_files $uri $uri/ /index.html; # Handle client-side routing

        # Cache static assets
        location /static {
            expires 1y;
            add_header Cache-Control "public, no-transform";
        }
    }

    # Node.js/Express API Backend
    location /api {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;

        # Add CORS headers
        add_header 'Access-Control-Allow-Origin' '*';
        add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS, PUT, DELETE';
        add_header 'Access-Control-Allow-Headers' 'DNT,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Range,Authorization';
    }

    # WebSocket Support (for features like Hot Module Replacement)
    location /ws {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}
```

## Node.js Application Deployment Best Practices

```bash
# Install PM2 for process management
sudo npm install -g pm2

# Start your Node.js application with PM2
pm2 start app.js --name "my-app"
pm2 startup # Generate startup script
pm2 save    # Save current process list
```

## Multiple JavaScript Applications Setup

```nginx
# For multiple Node.js applications or microservices
server {
    listen 80;
    server_name api.example.com;

    # Main API Service
    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}

server {
    listen 80;
    server_name app.example.com;

    # React/Vue/Angular Frontend
    location / {
        root /var/www/html/frontend/build;
        try_files $uri $uri/ /index.html;

        # Gzip compression for better performance
        gzip on;
        gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;
    }
}

server {
    listen 80;
    server_name admin.example.com;

    # Admin Dashboard
    location / {
        root /var/www/html/admin/dist;
        try_files $uri $uri/ /index.html;
    }
}
```

## Load Balancing Node.js Applications

```nginx
# Upstream configuration for load balancing
upstream node_backend {
    least_conn;  # Load balancing method
    server 127.0.0.1:3000;
    server 127.0.0.1:3001;
    server 127.0.0.1:3002;
}

server {
    listen 80;
    server_name api.example.com;

    location / {
        proxy_pass http://node_backend;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

## Security Headers for JavaScript Applications

```nginx
server {
    # ... existing configuration ...

    # Security headers
    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header X-Content-Type-Options "nosniff";
    add_header Referrer-Policy "strict-origin-when-cross-origin";
    add_header Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline' 'unsafe-eval'; style-src 'self' 'unsafe-inline';";

    # ... rest of your configuration ...
}
```

## 12. Nginx SSL Configuration

```bash
sudo apt install -y certbot python3-certbot-nginx # Install Certbot
sudo certbot --nginx # Generate SSL Certificate
```

## 13. Nginx SSL Configuration File

```bash
sudo nano /etc/nginx/sites-available/example.com # Open Nginx SSL Configuration File
```

```nginx
server {
    listen 443 ssl;
    server_name example.com www.example.com;

    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

## 15. Nginx SSL Configuration Reload

```bash
sudo systemctl reload nginx # Reload Nginx
```

## 16. Nginx SSL Renewal

```bash
sudo certbot renew # Renew SSL Certificate
```

## 17. Nginx SSL Renewal Test

```bash
sudo certbot renew --dry-run # Test SSL Certificate Renewal
```

---

# Nginx Multiple APP in multiple Sub Domain

```bash
sudo nano /etc/nginx/sites-available/[DOMAIN_NAME]
```

Add the following configuration:

```nginx
server {
    listen 80;
    server_name [Domain_NAME];

    location / {
        proxy_pass http://127.0.0.1:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}

server {
    listen 80;
    server_name [SUB_DOMAIN];

    location / {
        proxy_pass http://127.0.0.1:5000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

Enable the Sites

```bash
sudo ln -s /etc/nginx/sites-available/[DOMAIN_NAME] /etc/nginx/sites-enabled/
```

Test NGINX Config

```bash
sudo nginx -t
```

restart nginx server

```bash
sudo systemctl restart nginx
```
