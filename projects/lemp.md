## Introduction

In this guide, we will be configuring [Nginx](https://www.nginx.com/) [MariaDB](https://mariadb.org/) and [PHP](https://www.php.net/) to work with the [InCommon Certificate Authority](https://incommon.org/certificates/). We will be using Ubuntu 22.04 (Jammy Jellyfish)

### Install Nginx and MariaDB

```bash
sudo apt update && sudo apt install -y nginx mariadb-server
```

#### Configure MariaDB

Run the first-time setup for the MySQL installation. By default, it will ask for a root password, which is unset, so press enter.

Then it will ask if you want to set a root password, press `N`, and `ENTER`. For the rest of the prompts, press enter to accept the defaults. 

```bash
sudo mysql_secure_installation
```

Connect to your MariaDB installation

```bash
sudo mariadb
```

Create a new test database.

```sql
CREATE DATABASE example_db;
```

Grant privileges for a non-root user. Replace the username and password with your current username, and a secure password of your choosing.

```sql
GRANT ALL ON example_db.* TO 'username'@'localhost' IDENTIFIED BY 'password' WITH GRANT OPTION;
```

Flush privileges and exit.

```sql
FLUSH PRIVILEGES;
exit
```

Now connect again with the username you just created. It will prompt for the password.

```bash
mariadb -u username -p
```

Make sure the example_db is accessible.

```sql
SHOW DATABASES;
```

Then connect to the database.

```sql
USE example_db;
```

Create a table for later testing and exit.

```sql
CREATE TABLE table1(column1 varchar(255));
INSERT INTO table1 VALUES("Database connection established successfully");
exit
```

### Install PHP

Install php-fpm and php-mysql.

```bash
sudo apt install -y php-fpm php-mysql
```

#### Configure Nginx

Allow HTTP and HTTPS traffic through the UFW firewall.

```bash
sudo ufw allow http
sudo ufw allow https
```

Most of the steps below use the domain sandbox-fkayiwa, which you need to replace with your sandbox name.

Make a new directory for the website.

```
sudo mkdir -p /var/www/sandbox_fkayiwa
```
Give the correct user permissions to the website directory.

```bash
sudo chown -R $USER:$USER /var/www/sandbox_fkayiwa
```

Open a new file in the Nginx sites-available directory.

```bash
sudo vim /etc/nginx/sites-available/sandbox_fkayiwa
```

Add the following code snippet, save, and exit.

```conf
    server {
    listen 80;
    listen [::]:80;
    server_name sandbox-fkayiwa.lib.princeton.edu;
    root /var/www/sandbox_fkayiwa;
    location / {
        index index.html;
    }
}
```

Restart Nginx.

```bash
sudo systemctl restart nginx.service
```

Follow the instructions to get a [TLS certificate](https://github.com/pulibrary/pul-it-handbook/blob/main/services/create_ssl_certs.md) from OIT. When you received the signed certicates

Place the files under your nginx directory on your server with the following

```bash
sudo mkdir -p /etc/nginx/ssl{certs/private}
sudo mv sandbox-fkayiwa_lib_princeton_edu_chained.pem /etc/nginx/ssl/certs
sudo mv sandbox-fkayiwa_lib_princeton_edu_priv.key /etc/nginx/ssl/private
```

Open the Nginx config file again.

```bash
sudo vim /etc/nginx/sites-available/sandbox_fkayiwa
```

Replace everything with the following code snippet, save, and exit.

```conf
    server {
    listen 80;
    listen [::]:80;
    server_name sandbox-fkayiwa.lib.princeton.edu;
    root /var/www/sandbox_fkayiwa;
    location / {
        return 301 https://$server_name$request_uri;
    }
}
server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    ssl_certificate /etc/nginx/ssl/certs/sandbox-fkayiwa_lib_princeton_edu_chained.pem;
    ssl_certificate_key /etc/nginx/ssl/private/sandbox-fkayiwa_lib_princeton_edu_priv.key;
    root /var/www/sandbox-fkayiwa;
    index index.php index.html index.htm index.nginx-debian.html;
    add_header X-XSS-Protection "1; mode=block";
    add_header Content-Security-Policy "default-src 'self'; script-src 'self';";
    add_header Referrer-Policy "no-referrer";
    add_header X-Frame-Options "SAMEORIGIN" always;
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php8.1-fpm.sock;
    }
    server_name sandbox-fkayiwa.lib.princeton.edu;
    access_log /var/log/nginx/sandbox-fkayiwa.log;
    error_log /var/log/nginx/sandbox-fkayiwa.error.log;
}
```

Link the config file to the nginx sites-enabled directory.

```bash
sudo ln -s /etc/nginx/sites-available/sandbox_fkayiwa /etc/nginx/sites-enabled/
```

Restart nginx.

```bash
sudo systemctl restart nginx.service
```

### Test

Open a new file in your web directory.

```bash
vim /var/www/sandbox_fkayiwa/testdb.php
```

Add the following code snippet, save, and exit. Change the username and password below to the ones you set earlier in the MariaDB monitor.

```php
<?php
$mysqli = new mysqli("localhost", "username", "password", "example_db");

if (mysqli_connect_errno()) {
    printf("Connection failed: %s\n", mysqli_connect_error());
    exit();
}

$query = "SELECT column1 FROM table1";

if($result = $mysqli->query($query)) {
    while($row = $result->fetch_row()){
        printf("%s\n", $row[0]);
    }
    $result->close();
}

$mysqli->close();
?>
```

To verify the server is running with LetsEncrypt, and can access the database correctly, navigate to https://sandbox-fkayiwa.lib.princeton.edu/testdb.php, substituting with your domain.

You should see "Database connection established successfully". This verifies the LEMP stack is functioning properly.