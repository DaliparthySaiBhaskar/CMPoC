## Redis Configuration (RHEL-9)
### 1. Install Redis
Refer official documentation [here](https://redis.io/docs/latest/operate/oss_and_stack/install/install-redis/install-redis-on-linux/).  
```sh
sudo dnf install redis -y
```

### 4. Configure Redis for Remote Access
By default, Redis binds to `127.0.0.1`(localhost), restricting access to the local server. 
On some systems, Redis configuration might be stored in a different location, such as /etc/redis/redis.conf. You can try searching for the file using,
```sh
sudo find / -name redis.conf
```
```sh
sudo vim /etc/redis.conf
```
OR
```sh
sudo vim /etc/redis/redis.conf
```

Change the bind address from `127.0.0.1` to `0.0.0.0` to enable access from other servers.
```sh
bind 0.0.0.0
```

### 5. Start and Enable Redis Service
```sh
sudo systemctl start redis
```
```sh
sudo systemctl enable redis
```

### 6. Verify Redis Configuration
```sh
sudo systemctl status redis
```
Test remote connectivity using the redis-cli tool,
```sh
redis-cli -h <REDIS_SERVER_IP> -p 6379
```