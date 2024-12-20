## Payment Configuration (RHEL-9)
### 1. Install Python 3.6
```sh
sudo dnf install python3.11 gcc python3-devel -y
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
curl -L -o /tmp/payment.zip https://robotshop-builds.s3.us-east-1.amazonaws.com/payment.zip 
```
```sh
cd /app
```
```sh
sudo unzip /tmp/payment.zip
```

### 5. Install Application Dependencies
```sh
cd /app
```
```sh
pip3.11 install -r requirements.txt
```

### 6. Configure Systemd Service for Payment
```sh
sudo vim /etc/systemd/system/payment.service
```

Add the following configuration:
```sh
[Unit]
Description=Payment Service

[Service]
User=robotshop
WorkingDirectory=/app
Environment=CART_HOST=<CART-SERVER-IPADDRESS>
Environment=CART_PORT=8080
Environment=USER_HOST=<USER-SERVER-IPADDRESS>
Environment=USER_PORT=8080
Environment=AMQP_HOST=<RABBITMQ-SERVER-IPADDRESS>
Environment=AMQP_USER=robotshop
Environment=AMQP_PASS=robotshop123

ExecStart=/usr/local/bin/uwsgi --ini payment.ini
ExecStop=/bin/kill -9 $MAINPID
SyslogIdentifier=payment

[Install]
WantedBy=multi-user.target
```

**Note:** Replace `CART-SERVER-IPADDRESS`, `USER-SERVER-IPADDRESS`, and `RABBITMQ-SERVER-IPADDRESS`

### 7. Start and Enable Payment Service
```sh
sudo systemctl daemon-reload
```
```sh
sudo systemctl start payment
```
```sh
sudo systemctl enable payment
```

### 8. Update Frontend Configuration
Edit the Nginx configuration file.
```sh
sudo vim /etc/nginx/default.d/robotshop.conf
```
Update the payment service's IP address in the reverse proxy settings.
```sh
location /api/payment/ {
   proxy_pass http://<PAYMENT-SERVER-IP>:8080/;
}
```
**Note:** Replace `PAYMENT-SERVER-IP`