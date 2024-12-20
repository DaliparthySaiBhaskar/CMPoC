## RabbitMQ Configuration (RHEL-9)
Refer official documentation [here](https://www.rabbitmq.com/docs/install-rpm#rpm-version-locking). 

### 1. Configure YUM Repositories
**Add Erlang Repository**: Run the following script to set up the Erlang repository,
```sh
curl -s https://packagecloud.io/install/repositories/rabbitmq/erlang/script.rpm.sh | bash
```

**Add RabbitMQ Repository**: Run the following script to set up the RabbitMQ repository,
```sh
curl -s https://packagecloud.io/install/repositories/rabbitmq/rabbitmq-server/script.rpm.sh | bash
```

### 2. Install RabbitMQ
Install RabbitMQ along with its dependencies,
```sh
sudo dnf install rabbitmq-server -y
```

### 3. Start and Enable RabbitMQ Service
Enable RabbitMQ to start on boot and start the service,
```sh
sudo systemctl start rabbitmq-server
```
```sh
sudo systemctl enable rabbitmq-server
```

### 4. Create a Custom User for the Application
The default `guest` user is restricted to local connections. Create a dedicated user for the application.

**Add a New User**: 
```sh
sudo rabbitmqctl add_user robotshop robotshop123
```

**Set Permissions for the User**: 
Grant the new user full access,
```sh
sudo rabbitmqctl set_permissions -p / robotshop ".*" ".*" ".*"
```

### 5. Verify RabbitMQ Status
Check the status of RabbitMQ to ensure it's running properly.
```sh
sudo rabbitmqctl status
```