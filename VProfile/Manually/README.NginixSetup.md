# Set Up Nginx

Configure Nginx on `web01` as a reverse proxy to the Tomcat application on `app01`.

## Connect to the web VM

Connect to `web01` and switch to the root user:
```bash
vagrant ssh web01
 sudo -i
```

## Update and install Nginx

Update the operating system:
```bash
apt update
apt upgrade
```

Install Nginx:
```bash
apt install nginx -y
```

## Configure the reverse proxy

Create the `vproapp` site configuration:
```bash
vi /etc/nginx/sites-available/vproapp
```

Add this configuration to the file. It forwards requests to `app01` on port `8080`:
```ruby
upstream vproapp {
    server app01:8080;
    }

server {
    listen 80;
    location / {
        proxy_pass http://vproapp;
        }
    }
```

Remove the default site configuration:
```bash
rm -rf /etc/nginx/sites-enabled/default
```

Enable the `vproapp` site:
```bash
ln -s /etc/nginx/sites-available/vproapp /etc/nginx/sites-enabled/vproapp
```

Restart Nginx and check its status:
```bash
systemctl restart nginx
systemctl status nginx
```

When Tomcat and the other services are running, open the application at `http://192.168.56.11`.

Return to the [manual setup overview](README.md).
