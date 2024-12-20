## Cart Configuration (RHEL 9)
To ensure a required version is enabled, disable the default nodejs module. 
```sh
sudo dnf module disable nodejs -y
```
**Enable Node.js 20.X**:
```sh
sudo dnf module enable nodejs:20 -y
```

### 1. Install Nodejs
```sh
sudo dnf install nodejs -y
```

### 2. Create Application User
```sh
sudo useradd robotshop
```

### 3. Setup Application Directory
```sh
sudo mkdir /app
```

### 4. Download Application Code
```sh
curl -L -o /tmp/cart.zip https://robotshop-builds.s3.us-east-1.amazonaws.com/cart.zip
```
```sh
cd /app
```
```sh
sudo unzip /tmp/cart.zip
```

### 5. Install Application Dependencies
```sh
cd /app
```
```sh
npm install
```

### 6. Configure Systemd Service for Cart
To manage the Cart service with `systemctl`, create a `systemd` service file:
```sh
sudo vim /etc/systemd/system/cart.service
```

Add the following configuration:
```sh
[Unit]
Description=Cart Service

[Service]
User=robotshop
Environment=REDIS_HOST=<REDIS-SERVER-IP>
Environment=CATALOGUE_HOST=<CATALOGUE-SERVER-IP>
Environment=CATALOGUE_PORT=8080
ExecStart=/usr/bin/node /app/server.js
SyslogIdentifier=cart

[Install]
WantedBy=multi-user.target
```
**Note:** Replace `REDIS-SERVER-IP` and `CATALOGUE-SERVER-IP`

### 7. Enable and Start Cart Service
```sh
sudo systemctl daemon-reload
```
```sh
sudo systemctl enable cart
```
```sh
sudo systemctl start cart
```

### 8. Update Frontend Configuration
Edit the Nginx configuration file.
```sh
sudo vim /etc/nginx/default.d/robotshop.conf
```
Update the catalogue service's IP address in the reverse proxy settings.
```sh
location /api/cart/ {
   proxy_pass http://<CART-SERVER-IP>:8080/;
}
```
**Note:** Replace `CART-SERVER-IP`
