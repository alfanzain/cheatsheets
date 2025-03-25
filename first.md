# Background

This document serves as a **cheatsheet** for installing essential tools on Linux systems. The guide is designed to simplify the setup process for various development environments, whether you're preparing a system for **local development** or deploying applications on a **VPS (Virtual Private Server)**.

# Purpose:

1. **Cheatsheet for Quick Reference:** Provides step-by-step instructions to install and configure common tools and frameworks used in software development.
2. **Local Development Setup:** Enables developers to configure their Linux desktop or WSL environments for efficient coding and testing.
3. **VPS Deployment Guide:** Offers clear guidance for setting up tools and environments on VPS instances, ensuring readiness for production deployments.

This cheatsheet includes instructions for installing and configuring Git, Apache, Nginx, Golang, Node.js, PHP, Docker, and popular databases like PostgreSQL and MySQL, with additional tips for managing versions and environments.

# To-do List

- [ ] RabbitMQ
----
# Ubuntu

Last update: -
Last checked: -
Still works: **NOT SURE**

## Check Version

```bash
lsb_release -a
```

- **`lsb_release`** → Displays Linux Standard Base (LSB) information about the system.
- **`-a`** → Shows all available details.

## Add User

```bash
sudo adduser <username>
```

## Add User to Sudo Group

```bash
sudo usermod -aG sudo alfanzain
```

- **`sudo`** → Runs the command as an administrator (superuser).
- **`usermod`** → Modifies a user account.
- **`-aG`** →
    - `-a` (append) → Adds the user to a group without removing them from other groups.
    - `-G` (group) → Specifies the group(s) to add the user to.
- **`sudo`** → The name of the group being added.
- **`alfanzain`** → The username being modified.

## Change Password

```bash
passwd
```

## Verify the User

```bash
cat /etc/passwd | grep alfan
```

---
# Git

Last update: -
Last checked: -
Still works: **NOT SURE**

## Installation

```bash
sudo apt update
sudo apt install git
```

## Global User Config

```bash
git config --global user.email "you@example.com"
git config --global user.name "Alfan Zain"
```

---
# Apache

Last update: -
Last checked: -
Still works: **NOT SURE**

## References
- https://www.digitalocean.com/community/tutorials/how-to-install-the-apache-web-server-on-ubuntu-22-04
- 
## Installation

```bash
sudo apt update
sudo apt install apache2
```

---
# Nginx

Last update: -
Last checked: -
Still works: **NOT SURE**

## References
- https://www.digitalocean.com/community/tutorials/how-to-install-linux-nginx-mysql-php-lemp-stack-on-ubuntu#step-1-installing-the-nginx-web-server

## Installation

```bash
sudo apt update
sudo apt upgrade
sudo apt install nginx
```

```bash
sudo systemctl start nginx
sudo systemctl enable nginx
```

```bash
sudo cp path/to/your/index.html /var/www/html/index.html
```

If you have the `ufw` firewall enabled, as recommended in our initial server setup guide, you will need to allow connections to Nginx. Nginx registers a few different UFW application profiles upon installation. To check which UFW profiles are available, run:

```bash
sudo ufw app list
sudo ufw allow 'Nginx HTTP'
sudo ufw status
```

If you do not have a domain name pointed at your server and you do not know your server’s public IP address, you can find it by running either of the following commands:

```bash
ip addr show
hostname -I
```

## Configuration

When using the Nginx web server, we can create _server blocks_ (similar to virtual hosts in Apache) to encapsulate configuration details and host more than one domain on a single server.

On Ubuntu, Nginx has one server block enabled by default and is configured to serve documents out of a directory at `/var/www/html`. While this works well for a single site, it can become difficult to manage if you are hosting multiple sites. Instead of modifying `/var/www/html`, we’ll create a directory structure within `/var/www` for the **your_domain** website, leaving `/var/www/html` in place as the default directory to be served if a client request doesn’t match any other sites.

Create the root web directory

```bash
sudo mkdir /var/www/your_domain
```

Assign ownership of the directory with the `$USER` environment variable, which will reference your current system user

```bash
sudo chown -R $USER:$USER /var/www/your_domain
```

Open a new configuration file in Nginx’s `sites-available` directory

```bash
sudo nano /etc/nginx/sites-available/your_domain
```

In `/etc/nginx/sites-available/your_domain`

```bash
server {
    listen 80;
    server_name your_domain www.your_domain;
    root /var/www/your_domain;

    index index.html index.htm index.php;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
     }

    location ~ /\.ht {
        deny all;
    }

}
```

Activate your configuration by linking to the configuration file from Nginx’s `sites-enabled` directory

```bash
sudo ln -s /etc/nginx/sites-available/your_domain /etc/nginx/sites-enabled/
```

Test your configuration for syntax errors

```bash
sudo nginx -t
```

 Reload Nginx to apply the changes

```bash
sudo systemctl reload nginx
```

----

# Golang

Last update: -
Last checked: -
Still works: **NOT SURE****

## Installation

Download from here `https://go.dev/dl/` with `wget`. Usually the filename goes like this
`go<version>..linux-amd64.tar.gz`

```bash
cd /usr/local/

sudo wget https://go.dev/dl/go1.22.5.linux-amd64.tar.gz
sudo tar -xzf go1.22.5.linux-amd64.tar.gz
```

```bash
cd /etc/profile.d
sudo nano go.sh
```

`go.sh`

```bash
export PATH=$PATH:/usr/local/go/bin
export GOPATH=$HOME/go
```

After that exit the terminal to load the newly applied environment variables.

## Updating

```bash
cd /usr/local

sudo rm -rf go
repeat the process of installing new golang
```

## Golang Migrate

```bash
curl -L https://github.com/golang-migrate/migrate/releases/download/v4.15.2/migrate.linux-amd64.tar.gz -o migrate.tar.gz
```

```bash
tar -xvzf migrate.tar.gz
sudo mv migrate /usr/local/bin/
```

```bash
migrate -version
```

----

# NodeJS 

Last update: -
Last checked: -
Still works: **NOT SURE**

Using NVM

## References
- https://github.com/nvm-sh/nvm?tab=readme-ov-file#installing-and-updating (27 Jul 2024)

## Installation

```bash
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash

export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm

source ~/.bashrc

nvm install --lts
```

Latest LTS per 27 Jul 2024: `v20.16.0`

----
# PHP

Last update: -
Last checked: -
Still works: **NOT SURE**

> [!NOTE] Note
> Consider using asdf or something versioning for local development. Or seek for alternatives

## With Apache

TODO: Should separate this apache and php

```bash
sudo apt update
sudo apt install software-properties-common
sudo add-apt-repository ppa:ondrej/php
sudo apt update
```

```bash
sudo apt install php8.2 php8.2-cli php8.2-fpm php8.2-common php8.2-mbstring php8.2-xml php8.2-mysql php8.2-pgsql php8.2-zip php8.2-curl
sudo apt install php8.3 php8.3-cli php8.3-fpm php8.3-common php8.3-mbstring php8.3-xml php8.3-mysql php8.3-pgsql php8.3-zip php8.3-curl
```

```bash
sudo a2enmod proxy_fcgi setenvif
sudo a2enconf php8.2-fpm
sudo a2enconf php8.3-fpm
```

```bash
sudo mkdir -p /var/www/php83
sudo mkdir -p /var/www/php83
```

```bash
sudo nano /etc/apache2/sites-available/php83.conf

<VirtualHost *:80>
    ServerName php83.example.com
    DocumentRoot /var/www/php83/public

    <Directory /var/www/php83/public>
        AllowOverride All
        Require all granted
    </Directory>

    <FilesMatch \.php$>
        SetHandler "proxy:unix:/run/php/php8.3-fpm.sock|fcgi://localhost/"
    </FilesMatch>
</VirtualHost>
```

```bash
sudo a2ensite php82.conf
sudo a2ensite php83.conf
sudo systemctl restart apache2
```

Change version:

```bash
sudo update-alternatives --install /usr/bin/php php /usr/bin/php8.2 82
sudo update-alternatives --install /usr/bin/php php /usr/bin/php8.3 83
sudo update-alternatives --config php
```

----
# Composer

Last update: 2025-03-25
Last checked: 2025-03-25
Still works: **YES**

## Installation

```bash
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('sha384', 'composer-setup.php') === 'dac665fdc30fdd8ec78b38b9800061b4150413ff2e3b6f88543c636f7cd84f6db9189d43a81e5503cda447da73c7e5b6') { echo 'Installer verified'.PHP_EOL; } else { echo 'Installer corrupt'.PHP_EOL; unlink('composer-setup.php'); exit(1); }"
php composer-setup.php
php -r "unlink('composer-setup.php');"
```

```bash
sudo mv composer.phar /usr/local/bin/composer
```

----
# PostgreSQL

Last update: 2025-03-25
Last checked: 2025-03-25
Still works: **YES**

https://www.digitalocean.com/community/tutorials/how-to-install-postgresql-on-ubuntu-22-04-quickstart

## Installation

```bash
sudo apt update
sudo apt install postgresql postgresql-contrib postgresql-common
```

## User Configuration

```bash
sudo -i -u postgres

psql

\q

exit
```

```bash
sudo -u postgres psql
createuser --interactive
# or
sudo -u postgres createuser --interactive
```

```bash
createdb sammy
# or
sudo -u postgres createdb sammy
```

Change password

```bash
sudo -u postgres psql

ALTER USER postgres PASSWORD 'newpassword';
```

## PostGIS

```bash
sudo apt install postgis
sudo apt install postgresql-14-postgis-scripts
```

```bash
# Switch to desired database
CREATE SCHEMA postgis;
CREATE EXTENSION postgis SCHEMA postgis;
```

----
# MySQL

Last update: 
Last checked: 
Still works: 

References:
- https://docs.vultr.com/how-to-install-mysql-on-ubuntu-24-04

## Installation

```bash
sudo apt update
sudo apt install mysql-server -y
```

```bash
mysql --version

sudo apt install mysql-server -y

sudo systemctl start mysql
sudo systemctl status mysql
```

## Secure the MySQL Server

```bash
sudo mysql_secure_installation

sudo systemctl start mysql
```

#### **Why should you do this?**
- **Removes anonymous users** → Prevents unauthorized logins.
- **Disables remote root login** → Blocks external root access for security.
- **Removes test databases** → Deletes default databases that could be exploited.
- **Enforces a strong root password** → Ensures only authorized access.

## Access MySQL

```bash
sudo mysql -u root -p
```

```mysql
CREATE DATABASE my_database;
CREATE USER 'my_user'@'localhost' IDENTIFIED BY 'my_password';
GRANT ALL PRIVILEGES ON my_database.* TO 'my_user'@'localhost';
FLUSH PRIVILEGES;
```

----
# Docker

Last update: \
Last checked: \
Still works: 

## Installation

```bash
sudo apt update
sudo apt install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null4
```

```bash
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io
docker --version
```

```bash
sudo apt update
sudo apt install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

```bash
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io
docker --version
```

## Commands

```bash
docker compose start
docker compose stop
docker compose pause
docker compose unpause
docker compose ps
docker compose up
docker compose down
```

----
# Redis

Last update: 
Last checked: 
Still works: 

## Installation

```bash
curl -fsSL https://packages.redis.io/gpg | sudo gpg --dearmor -o /usr/share/keyrings/redis-archive-keyring.gpg

echo "deb [signed-by=/usr/share/keyrings/redis-archive-keyring.gpg] https://packages.redis.io/deb $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/redis.list

sudo apt-get update
sudo apt-get install redis

sudo service redis-server start
```
## Connect

```bash
redis-cli

127.0.0.1:6379> ping
```

```bash
#Output
PONG
```
