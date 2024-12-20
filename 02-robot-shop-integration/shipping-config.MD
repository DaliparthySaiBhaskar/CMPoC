## Shipping Configuration (RHEL-9)
### 1. Install Maven (and Java)
Maven, a Java packaging tool, will automatically install Java as a dependency.
```sh
sudo dnf install maven -y
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
curl -L -o /tmp/shipping.zip https://robotshop-builds.s3.us-east-1.amazonaws.com/shipping.zip 
```
```sh
cd /app
```
```sh
sudo unzip /tmp/shipping.zip
```

### 5. Install Dependencies and Build the Application
```sh
cd /app
```
```sh
mvn clean package
```

After building, rename the generated JAR file for easier access:
```sh
mv target/shipping-1.0.jar shipping.jar
```

### 6. Configure Systemd Service for Shipping
To manage the Shipping service with `systemctl`, create a `systemd` service file:
```sh
sudo vim /etc/systemd/system/shipping.service
```

Add the following configuration:
```sh
[Unit]
Description=Shipping Service

[Service]
User=robotshop
Environment=CART_ENDPOINT=<CART-SERVER-IPADDRESS>:8080
Environment=DB_HOST=<MYSQL-SERVER-IPADDRESS>
ExecStart=/usr/bin/java -jar /app/shipping.jar
SyslogIdentifier=shipping

[Install]
WantedBy=multi-user.target
```
**Note:** Replace `CART-SERVER-IPADDRESS` and `MYSQL-SERVER-IPADDRESS`

### 7. Enable and Start Shipping Service
Reload the systemd daemon to apply the new service configuration:
```sh
sudo systemctl daemon-reload
```
```sh
sudo systemctl enable shipping
```
```sh
sudo systemctl start shipping
```

### 8. Load MySQL Schema
The Shipping service requires a specific schema to function. Install the MySQL client and load the schema into MySQL:

Install the MySQL client:
```sh
sudo dnf install mysql -y
```

Load the schema:
```sh
mysql -h <MYSQL-SERVER-IPADDRESS> -uroot -pRoboShop@1 < /app/db/schema.sql
```
```sh
mysql -h <MYSQL-SERVER-IPADDRESS> -uroot -pRoboShop@1 < /app/db/app-user.sql 
```
```sh
mysql -h <MYSQL-SERVER-IPADDRESS> -uroot -pRoboShop@1 < /app/db/master-data.sql
```
OR
```sh
mysql --default-auth=mysql_native_password -h <MYSQL-SERVER-IPADDRESS> -uroot -pRobotShop@1 < /app/schema/shipping.sql
```
**Note:** Replace `MYSQL-SERVER-IPADDRESS`

### 9. Restart Shipping Service
```sh
sudo systemctl restart shipping
```

### 10. Update Frontend Configuration
Edit the Nginx configuration file.
```sh
sudo vim /etc/nginx/default.d/robotshop.conf
```
Update the shipping service's IP address in the reverse proxy settings.
```sh
location /api/shipping/ {
   proxy_pass http://<SHIPPING-SERVER-IP>:8080/;
}
```
**Note:** Replace `SHIPPING-SERVER-IP`