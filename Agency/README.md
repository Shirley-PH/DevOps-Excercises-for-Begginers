# Tween Agency Website Manually

![Preview of the Tween Agency website](agency-tween-page.png)

This guide explains how I manually set up a local copy of a website template using Vagrant. It shares what I have learned so far. My next step is to automate the setup.

Vagrant creates a virtual machine: a separate, software-based computer that runs on your own computer. We will use it to run the website without changing the setup of the main computer.

## Before you begin

Install Vagrant and a provider such as VirtualBox. A provider is the software that runs the virtual machine.

## 1. Create and start the virtual machine

On your computer, open a terminal such as PowerShell, move to the folder where you want to keep this project, and run:

```bash
mkdir website-demo
cd website-demo
vagrant init eurolinux-vagrant/centos-stream-9
```

Open the new `Vagrantfile` in a text editor. Inside the existing `Vagrant.configure` block, add this line:

```ruby
config.vm.network "forwarded_port", guest: 80, host: 8080
```

This makes the website available on your computer at port `8080`. Save the file, return to the terminal, and start the virtual machine:

```bash
vagrant up
vagrant ssh
```

The `vagrant ssh` command opens a terminal inside the virtual machine. Run the remaining commands there.

## 2. Install and start the website software

Apache is the software that serves the website files to your browser. Install Apache and the tools needed to download and unpack the template:

```bash
sudo dnf install -y httpd wget unzip vim
sudo systemctl enable --now httpd
```

The second command starts Apache now and sets it to start automatically when the virtual machine starts.

## 3. Download and install the website template

For this example, I chose the [Tween Agency template from Tooplate](https://www.tooplate.com/view/2128-tween-agency). Download and unpack it inside the virtual machine:

```bash
cd /tmp
wget https://www.tooplate.com/zip-templates/2128_tween_agency.zip
unzip 2128_tween_agency.zip
```

Copy the template files into Apache's website folder:

```bash
sudo cp -r 2128_tween_agency/. /var/www/html/
```

Apache uses `/var/www/html/` as its default website folder.


## 5. View the website

On your computer, open a browser and go to [http://localhost:8080](http://localhost:8080). You should see the Tween Agency template.

If the page does not load, check that the virtual machine is running (`vagrant up`) and that Apache is active (`sudo systemctl status httpd` inside the virtual machine).
