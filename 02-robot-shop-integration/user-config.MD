## User Configuration (RHEL-9)
### 1. Install Node.js
Ensure the required version of Node.js is installed.
*you can list modules by using dnf module list*

**Disable the default version:**
```sh
sudo dnf module disable nodejs -y
```
**Enable the desired version (e.g., Node.js 20)**
```sh
sudo dnf module enable nodejs:20 -y
```
**Install Node.js**
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

### 4.  Download Application Code
```sh
curl -L -o /tmp/user.zip https://robotshop-builds.s3.us-east-1.amazonaws.com/user.zip  
```
```sh
cd /app
```
```sh
sudo unzip /tmp/user.zip
```

### 5. Install Application Dependencies
```sh
cd /app
```
```sh
npm install
```

### 6. Configure Systemd Service for User
To manage the User service with `systemctl`, create a `systemd` service file:
```sh
sudo vim /etc/systemd/system/user.service
```

Add the following configuration (replace REDIS-SERVER-IP and MONGODB-SERVER-IP-ADDRESS with actual values)
```sh
[Unit]
Description=User Service

[Service]
User=robotshop
Environment=MONGO=true
Environment=REDIS_HOST=<REDIS-SERVER-IP>
Environment=MONGO_URL="mongodb://<MONGODB-SERVER-IP-ADDRESS>:27017/users"
ExecStart=/usr/bin/node /app/server.js
SyslogIdentifier=user

[Install]
WantedBy=multi-user.target
```

**Note:** Replace `REDIS-SERVER-IP` and `MONGODB-SERVER-IP-ADDRESS`

### 7. Start and Enable User Service
Reload the systemd daemon to apply the new service configuration:
```sh
sudo systemctl daemon-reload
```
Start the User service:
```sh
sudo systemctl start user
```
Enable the User service to start on boot:
```sh
sudo systemctl enable user
```

### 8. Configure MongoDB Repository, Install MongoDB Shell, and and Load Schema
**Step 1**: Ensure the MongoDB repository is configured
```sh
sudo vim /etc/yum.repos.d/mongo.repo
```

Add the following content:
```sh
[mongodb-org-7.0]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/9/mongodb-org/7.0/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://pgp.mongodb.com/server-7.0.asc
```

**Step 2**: Install the MongoDB shell
```sh
sudo dnf install mongodb-mongosh -y
```

**Load the User Schema:**  Load the schema into MongoDB
```sh
mongosh --host MONGODB-SERVER-IPADDRESS </app/schema/user.js
```
**Note:** Replace `MONGODB-SERVER-IP-ADDRESS` 

### 9. Update Frontend Configuration
Edit the Nginx configuration file.
```sh
sudo vim /etc/nginx/default.d/robotshop.conf
```
Update the catalogue service's IP address in the reverse proxy settings.
```sh
location /api/user/ {
   proxy_pass http://<USER-SERVER-IP>:8080/;
}
```
**Note:** Replace `CATALOGUE-SERVER-IP` 
