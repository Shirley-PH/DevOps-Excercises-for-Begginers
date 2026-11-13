# Install and Configure Tomcat

Run these steps on `app01`, the application VM. This guide installs Java 17 and Apache Tomcat 10.1.26, then configures Tomcat to run as a system service.

## Connect to the application VM

Connect to `app01`:
```bash
vagrant ssh app01
```

## Install Java and required packages

Update the operating system:
```bash
dnf update -y
```

Enable the EPEL repository:
```bash
dnf install epel-release -y
```

Install Java and the required tools:
```bash
 dnf -y install java-17-openjdk java-17-openjdk-devel
 dnf install git wget -y
```

## Download and extract Tomcat

Download Tomcat 10.1.26:
```bash
wget https://archive.apache.org/dist/tomcat/tomcat-10/v10.1.26/bin/apache-tomcat-10.1.26.tar.gz
```

Extract the archive:
```bash
tar xzvf apache-tomcat-10.1.26.tar.gz
```

Create a dedicated Tomcat user:
```bash
useradd --home-dir /usr/local/tomcat --shell /sbin/nologin tomcat
```

Copy the extracted files to Tomcat's home directory:
```bash
cp -r /tmp/apache-tomcat-10.1.26/* /usr/local/tomcat/
```

Set the Tomcat user as the owner of its installation directory:
```bash
chown -R tomcat.tomcat /usr/local/tomcat
```

## Configure Tomcat as a system service

Create the systemd service file:
```bash
vi /etc/systemd/system/tomcat.service
```

Add this service configuration to the file:
```Ruby
[Unit]
Description=Tomcat
After=network.target

[Service]
User=tomcat
Group=tomcat
WorkingDirectory=/usr/local/tomcat
Environment=JAVA_HOME=/usr/lib/jvm/jre
Environment=CATALINA_PID=/var/tomcat/%i/run/tomcat.pid
Environment=CATALINA_HOME=/usr/local/tomcat
Environment=CATALINE_BASE=/usr/local/tomcat
ExecStart=/usr/local/tomcat/bin/catalina.sh run
ExecStop=/usr/local/tomcat/bin/shutdown.sh
RestartSec=10
Restart=always

[Install]
WantedBy=multi-user.target
```

Reload the systemd configuration:
```bash
systemctl daemon-reload
```

Start and enable Tomcat:
```bash
systemctl start tomcat
systemctl enable tomcat
```

After Tomcat is running, continue to [build and deploy the application](README.Building%26Deploy.md).

Return to the [manual setup overview](README.md).

