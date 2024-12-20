## Web Configuration (CentOS-8 / RHEL-9)
### 1. Install Nginx
```sh
sudo dnf install nginx -y
```

### 2. Enable & Start Nginx service 
```sh
sudo systemctl enable nginx
```
```sh
sudo systemctl start nginx
```

### 3. Verify Nginx Installation
Open a web browser and visit the following URL to ensure Nginx is serving the default content. 
```sh
http://<your-public-IP>:80
```

### 4. Remove Default Content
```sh
sudo rm -rf /usr/share/nginx/html/*
```

### 5. Download RoboShop Frontend Content
```sh
curl -o /tmp/web.zip https://robotshop-builds.s3.us-east-1.amazonaws.com/web.zip 
```

### 6. Extract Frontend Content
```sh
sudo unzip /tmp/web.zip -d /usr/share/nginx/html/
```

### 7. Configure Nginx as a Reverse Proxy
Create a `reverse proxy configuration` to forward API requests to the appropriate backend services. [[Official Documentation]](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/9/html/deploying_web_servers_and_reverse_proxies/setting-up-and-configuring-nginx_deploying-web-servers-and-reverse-proxies#setting-up-and-configuring-nginx_deploying-web-servers-and-reverse-proxies)

Open the Nginx configuration file,
```sh
sudo vim /etc/nginx/default.d/robotshop.conf
```

Add the following configuration,
```sh
proxy_http_version 1.1;

location /images/ {
  expires 5s;
  root   /usr/share/nginx/html;
  try_files $uri /images/placeholder.jpg;
}

location /api/catalogue/ { proxy_pass http://localhost:8080/; }
location /api/user/ { proxy_pass http://localhost:8080/; }
location /api/cart/ { proxy_pass http://localhost:8080/; }
location /api/shipping/ { proxy_pass http://localhost:8080/; }
location /api/payment/ { proxy_pass http://localhost:8080/; }

location /health {
  stub_status on;
  access_log off;
}
```
Make sure to replace `localhost` with the actual `IP addresses` or `DNS` of the respective component services. 

### 8. Restart Nginx
```sh
sudo systemctl restart nginx 
```