## MongoDB Configuration (RHEL-9)
### 1. Install MongoDB Community Server
MongoDB isn’t included in CentOS/RHEL repositories, so we need to configure the official MongoDB repository.

**Add MongoDB Repository**:

Create a repository file,
```sh
sudo vim /etc/yum.repos.d/mongo.repo
```
Add the following configuration,
```sh
[mongodb-org-7.0]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/9/mongodb-org/7.0/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://pgp.mongodb.com/server-7.0.asc
```
Refer to MongoDB’s [[Community]](https://www.mongodb.com/docs/manual/tutorial/install-mongodb-on-red-hat/) or [[Enterprise]](https://www.mongodb.com/docs/manual/tutorial/install-mongodb-enterprise-on-red-hat/) documentation for more.

**Install MongoDB**:
```sh
sudo dnf install mongodb-org -y 
```

### 3. Start & Enable MongoDB service
Start the service and Enable MongoDB to start on boot,
```sh
sudo systemctl start mongod
```
```sh
sudo systemctl enable mongod
```
### 4. Allow External Connections
By default, MongoDB binds to `127.0.0.1`(localhost), restricting access to the local server only. To allow access from remote servers, update the configuration.

Edit MongoDB Configuration,
```sh
sudo vim /etc/mongod.conf
```
Update the `net` section to allow connections from any IP address,
```sh
# network interfaces
net:
  port: 27017
  bindIp: 0.0.0.0  # Enable external access
```

### 5. Restart MongoDB
```sh
sudo systemctl restart mongod
```
Check if MongoDB is running,
```sh
sudo systemctl status mongod
```

### 6. Ensure Firewall Rules Allow Connections
CentOS-8/RHEL-9 uses firewalld by default. Open the MongoDB port (`27017`) in the firewall.
```sh
sudo firewall-cmd --add-port=27017/tcp --permanent
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

### 7. Verify 
Check if MongoDB is listening,
```sh
netstat -nltp
```
```sh
ping <MONGODB-SERVER-IP>
```

### 8. Test Remote Connectivity
Install MongoDB Shell
```sh
sudo dnf install -y mongodb-mongosh
```
Test connection from a remote server
```sh
mongosh --host <MONGODB-SERVER-IP> --port 27017
```