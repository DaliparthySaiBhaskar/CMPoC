## Catalogue Configuration (RHEL-9)
The Catalogue service is a Node.js-based microservice that interacts with MongoDB to manage product information. Below are the steps to install, configure, and integrate the Catalogue service.

### 1. Install Node.js
Ensure the required Node.js version is installed.

**Disable the default Node.js module**
```sh
sudo dnf module disable nodejs -y
```
**Enable the required version (e.g., Node.js 20)**
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

### 4. Download Application Code
Download the application code to the newly created /app directory.
```sh
curl -o /tmp/catalogue.zip https://robotshop-builds.s3.us-east-1.amazonaws.com/catalogue.zip  
```
**Extract the code**
```sh
cd /app
```
```sh
sudo unzip /tmp/catalogue.zip
```

### 5. Install Application Dependencies
```sh
cd /app
```
```sh
npm install
```

### 6. Configure Systemd Service for Catalogue
Create a `systemd` service file to manage the Catalogue service
```sh
sudo vim /etc/systemd/system/catalogue.service
```

Add the following content:
```sh
[Unit]
Description=Catalogue Service

[Service]
User=robotshop
Environment=MONGO=true
Environment=MONGO_URL="mongodb://<MONGODB-SERVER-IP>:27017/catalogue"
ExecStart=/usr/bin/node /app/server.js
SyslogIdentifier=catalogue

[Install]
WantedBy=multi-user.target
```
**Note:** Replace `MONGODB-SERVER-IP`

### 7. Start and Enable the Catalogue Service
```sh
sudo systemctl daemon-reload
```
```sh
sudo systemctl start catalogue
```
```sh
sudo systemctl enable catalogue
```


### 8. Configure MongoDB Repository and Install MongoDB Shell
The Catalogue service requires an initial schema to be loaded into MongoDB for proper operation. To accomplish this, first configure the MongoDB repository and install the MongoDB shell client, which will allow us to interact with the MongoDB server from the command line.

**Step 1**: Configure the MongoDB repository if it hasn’t been done already:
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

**Step 2**: Install MongoDB Shell Install the MongoDB shell to allow command-line interactions with the MongoDB database.
```sh
sudo dnf install -y mongodb-mongosh
```

### 9. Load the Catalogue Schema into MongoDB
Once the MongoDB shell is installed, use it to load the initial product schema into MongoDB. This schema file (`catalogue.js` in the `/app/schema/` folder) sets up the structure or default data needed by the Catalogue service.

Execute the following command to load the schema:
```sh
mongosh --host MONGODB-SERVER-IPADDRESS </app/schema/catalogue.js
```

**Note**: Replace `MONGODB-SERVER-IP`

This command connects to `MongoDB` and runs the commands in `catalogue.js`, `creating collections`, `setting up fields`, or `inserting sample data` as needed by the application.

### 10. Troubleshooting Steps
```sh
netstat -nltp
```
```sh
# To get running logs
tail -n 10 /var/log/messages
```
```sh
# To get all logs
less /var/log/messages
```

### 11. Update Frontend Configuration
To make the Catalogue service accessible from the frontend, update the Nginx configuration:

Edit the Nginx configuration file.
```sh
sudo vim /etc/nginx/default.d/robotshop.conf
```
Update the catalogue service's IP address in the reverse proxy settings.
```sh
location /api/catalogue/ {
   proxy_pass http://<CATALOGUE-SERVER-IP>:8080/;
}
```
**Note:** Replace `CATALOGUE-SERVER-IP`