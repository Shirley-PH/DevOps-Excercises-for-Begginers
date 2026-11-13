# Set Up MariaDB for VProfile

## Connect to the database VM

Connect to `db01`:
```bash
vagrant ssh db01
```

## Install and start MariaDB

Update the operating system:
```bash
dnf update -y
```

Enable the EPEL repository:
```bash
dnf install epel-release -y
```

Install Git and MariaDB:
```bash
dnf install git mariadb-server -y
```

Start and enable MariaDB:
```bash
systemctl start mariadb
systemctl enable mariadb
```

Run the secure installation script and set a root password when prompted:
```bash
mysql_secure_installation

```

## Create the database and application user

Log in to MariaDB using the root password you set. Create the `accounts` database and grant the `admin` user access from the database VM and other hosts. Replace the example password with the password you chose:

```bash
mysql> create database accounts;
mysql> grant all privileges on accounts.* TO 'admin'@'localhost' identified by
'changeforActualPassowrd'; 
mysql> grant all privileges on accounts.* TO 'admin'@'%' identified by 'changeforActualPassowrd'; 
mysql> FLUSH PRIVILEGES;
mysql> exit;
```
## Load the application database

Clone the application's `local` branch:
```bash
cd /tmp
git clone -b local https://github.com/hkhcoder/vprofile-project.git
```
Change to the cloned repository:
```bash
cd vprofile-project
mysql -u root -pchangeforActualPassowrd accounts < src/main/resources/db_backup.sql
mysql -u root -pchangeforActualPassowrd accounts
```
Verify that the database and tables are available:

```bash
mysql> show tables;
mysql> show databases;
mysql> exit;
```

## Allow database connections

Allow MariaDB connections on TCP port `3306` so that `app01` can connect to the database. Configure the firewall on `db01` accordingly.