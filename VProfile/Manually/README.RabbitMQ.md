# Set Up RabbitMQ

## Connect to the message-broker VM

Connect to `rmq01`:
```bash
vagrant ssh rmq01
```

## Install RabbitMQ

Update the operating system:
```bash
dnf update -y
```

Enable the EPEL repository:
```bash
dnf install epel-release -y
```

Install and enable RabbitMQ:
```bash
 sudo dnf install wget -y
 dnf -y install centos-release-rabbitmq-38
 dnf --enablerepo=centos-rabbitmq-38 -y install rabbitmq-server
 systemctl enable --now rabbitmq-server
```

## Configure the RabbitMQ user

The following commands configure remote access and create the example `test` user with administrator privileges:
```bash
sudo sh -c 'echo "[{rabbit, [{loopback_users, []}]}]." > /etc/rabbitmq/rabbitmq.config'
sudo rabbitmqctl add_user test test
sudo rabbitmqctl set_user_tags test administrator
rabbitmqctl set_permissions -p / test ".*" ".*" ".*"
sudo systemctl restart rabbitmq-server
```

These settings and credentials are for an isolated lab only. Use strong credentials and restrict access to the private network in other environments.

Return to the [manual setup overview](README.md).

