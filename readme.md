# 🚀 YGS-Shop-Webapp

## ⚠️ Important Notice
**Don't touch any code if you don't know what you are doing.**

## 📋 Installation Guide

### 1️⃣ Prerequisites
- Install Python
- Install Nginx

### 2️⃣ Set Up Virtual Environment
```bash
# Create a virtual environment
python3 -m venv .venv

# Activate the virtual environment
source .venv/bin/activate
```

### 3️⃣ Install Dependencies
```bash
# Install all required packages
pip3 install -r requirements.txt
```

### 4️⃣ Configure Application
```bash
# Run Gunicorn with one worker in the project folder
gunicorn -w <worker_count> main:app

# Complete the configuration as prompted
```

### 5️⃣ Nginx Configuration

Create a new Nginx site configuration:
```bash
nano /etc/nginx/sites-available/<name>
```

Add the following configuration:

```nginx
# HTTP server - redirects to HTTPS
server {
    listen 80;
    server_name <domain>;
    location / {
        return 301 https://$host$request_uri;
    }
}

# HTTPS server
server {
    listen 443 ssl;
    server_name <domain>;
    
    # SSL certificate paths
    ssl_certificate /etc/letsencrypt/live/<domain>/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/<domain>/privkey.pem;
    
    # SSL settings
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers on;
    ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384;
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
    
    # Proxy headers
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
    
    # Web root location
    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_redirect off;
        proxy_buffering off;
    }
    
    # Static files location (assuming your Flask app serves static files from /static)
    location /static/ {
        alias /path/to/your/flask/app/static/;
        expires 30d;
    }
    
    # Error pages
    error_page 404 /404.html;
    error_page 500 502 503 504 /50x.html;
    
    # Logging
    access_log /var/log/nginx/<domain>.access.log;
    error_log /var/log/nginx/<domain>.error.log;
}
```

### 6️⃣ Enable and Test Configuration
```bash
# Create symbolic link to enable the site
sudo ln -s /etc/nginx/sites-available/<name> /etc/nginx/sites-enabled/

# Test Nginx configuration
sudo nginx -t

# If everything works, reload Nginx
systemctl reload nginx
```

### 7️⃣ SSL Certificate (Optional)
```bash
# Install Certbot
apt install certbot

# Obtain SSL certificate
sudo certbot --nginx -d <domain_name>
```

## 🔄 Final Steps
Your YGS-Shop-Webapp should now be running successfully!