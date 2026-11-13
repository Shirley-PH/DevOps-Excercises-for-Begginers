# Set Up Memcached

## Connect to the cache VM

Connect to `mc01`:
```bash
vagrant ssh mc01
```

## Update the operating system

Update the operating system:
```bash
dnf update -y
```

The original steps repeat the update command; it is retained here:
```bash
dnf update -y
```

## Install and start Memcached

Install Memcached, start the service, and configure it to accept connections from the application VM:
```bash
sudo dnf install epel-release -y
sudo dnf install memcached -y
sudo systemctl start memcached
sudo systemctl enable memcached
sudo systemctl status memcached
sed -i 's/127.0.0.1/0.0.0.0/g' /etc/sysconfig/memcached #Explain why we are going this change
sudo systemctl restart memcached
```

Review the Memcached configuration and confirm that its listen address allows connections from `app01`:
```bash
cat /etc/sysconfig/memcached
PORT="11211"
USER="memcached"
MAXCONN="1024"
CACHESIZE="64"
OPTIONS="-l 127.0.0.0,::1"
```

Memcached listens on TCP port `11211`. Restrict access to the private lab network.

Return to the [manual setup overview](README.md).
