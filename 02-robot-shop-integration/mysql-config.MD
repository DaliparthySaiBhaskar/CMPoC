## MySQL Configuration (RHEL-9)
### Installation options
Refer to MySQL [Getting Started](https://dev.mysql.com/doc/mysql-getting-started/en/) for official installation methods including,
-  [MySQL Yum Repository](https://dev.mysql.com/doc/mysql-yum-repo-quick-guide/en/)

- [RPM Packages](https://dev.mysql.com/doc/refman/8.0/en/linux-installation-rpm.html)

### 1. Install MySQL
```sh
sudo dnf install mysql-server -y
```

### 4. Start and Enable MySQL Service
```sh
systemctl start mysqld
```
```sh
systemctl enable mysqld
```

### 5. Configure Root Password

Change the default root password in order to start using the service. Set a secure root password (e.g., RobotShop@1).
```sh
mysql_secure_installation --set-root-pass RobotShop@1
```

**Verify the root login**

Test the new root password to ensure it's working.
```sh
mysql -uroot -pRobotShop@1
```
**NOTE**: Setting the MySQL root password and verifying login are essential for securing and gaining access to MySQL as an administrator. Also to make sure MySQL is securely configured and accessible.

### 6. Allow External Connections
By default, MySQL binds to 127.0.0.1. Update the configuration to allow remote access.

**Edit MySQL Configuration**: 
- Find or create the configuration file (typically /etc/my.cnf). Run the following command to find my.cnf or similar configuration files.
```sh
sudo find / -name "my.cnf"
```
- Check MySQL Default Config Directory, usually located at `/etc/my.cnf` or `/etc/mysql/my.cnf` or `/etc/mysql/mysql.conf.d/`

- If no configuration file is found, you can Create a Custom Configuration File in `/etc/`.
```sh
sudo vim /etc/my.cnf
```
- Add the following lines to set MySQL to listen on all IP addresses
```sh
[mysqld]
bind-address = 0.0.0.0
```
Restart MySQL Service,
```sh
sudo systemctl restart mysqld
```

### 7. Verify Installation and Connectivity
Check MySQL status,
```sh
sudo systemctl status mysqld
```
```sh
netstat -nltp | grep mysqld
```

### 8. Optional: Troubleshooting Authentication Issues
```sh
sudo mysql -uroot -p
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'RobotShop@1'; FLUSH PRIVILEGES;
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'RobotShop@1'; FLUSH PRIVILEGES;
```