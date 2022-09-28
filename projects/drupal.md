## Install Drupal 9 with Nginx on Ubuntu

In this guide, you'll learn how to install Drupal 9 with Nginx and a [LEMP](lemp.md) stack on Ubuntu

#### Configure Nginx for Drupal

Drupal depends on the same Nginx, MariaDB and PHP. After installing the [LEMP](lemp.md) stack, you'll need to set up a few things to get Drupal running.

Create a new Nginx server configuration block to use PHP Processor.

```bash
sudo vim /etc/nginx/sites-available/drupal_sandbox_fkayiwa
```

Create a configuration like the one below:


```conf
server {
    listen 80;
    listen [::]:80;
    server_name sandbox-fkayiwa.lib.princeton.edu;
    root /var/www/html/drupal;
    location / {
        return 301 https://$server_name$request_uri;
    }
}
server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    ssl_certificate /etc/nginx/ssl/certs/sandbox-fkayiwa_lib_princeton_edu_chained.pem;
    ssl_certificate_key /etc/nginx/ssl/private/sandbox-fkayiwa_lib_princeton_edu_priv.key;
    root /var/www/html/drupal;
    location / {
        try_files $uri $uri/ /index.php$is_args$args;
    }
    location = /favicon.ico { log_not_found off; access_log off; }
    location = /robots.txt { log_not_found off; access_log off; allow all; }
    location ~* \.(css|gif|ico|jpeg|jpg|js|png)$ {
        expires max;
        log_not_found off;
    }
    index index.php index.html index.htm index.nginx-debian.html;
    add_header X-XSS-Protection "1; mode=block";
    add_header Content-Security-Policy "default-src 'self'; script-src 'self';";
    add_header Referrer-Policy "no-referrer";
    add_header X-Frame-Options "SAMEORIGIN" always;
    location ~ \.php$ {
#       try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php8.1-fpm.sock;
#       fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
    server_name sandbox-fkayiwa.lib.princeton.edu;
    access_log /var/log/nginx/sandbox_fkayiwa.log;
    error_log /var/log/nginx/sandbox_fkayiwa.error.log;
}
```

Test Nginx configuration.

```bash
sudo nginx -t
```

Restart the Nginx service to apply the changes.

```bash
sudo systemctl restart nginx
```

#### Configure MariaDB for Drupal

Connect to MariaDB as root.

```bash
sudo mariadb
```

Create a database named drupal.

```sql
CREATE DATABASE drupal;
```

Grant all privileges to database drupal.

```sql
GRANT ALL ON drupal.* TO 'drupal'@'localhost' IDENTIFIED BY 'StrongPassword';
```

Reload the changes.

```sql
FLUSH PRIVILEGES;
```

Exit MariaDB.

```bash
\q
```

#### Install Drupal

Install unzip.

```bash
sudo apt install unzip
```

#### Install Drupal Dependencies

Drupal expects the following php extensions:

```bash
sudo apt -y install php-dompdf php-xml php8.1-mbstring
```


Download the latest version of the Drupal from Drupal download page.

```bash
wget https://ftp.drupal.org/files/projects/drupal-9.4.5.zip
```

Extract the compressed file and browse to it.

```bash
sudo unzip drupal-9.4.5.zip
cd drupal-9.4.5
```

Create directory /var/www/html/drupal.

```bash
sudo mkdir -p /var/www/html/drupal
```

Move all the files within the current directory to /var/www/html/drupal directory.

```bash
sudo mv * /var/www/html/drupal
```

Go to /var/www/html/drupal/sites/default directory and copy the default.settings.php configuration file to settings.php.

```bash
cd /var/www/html/drupal/sites/default
sudo cp default.settings.php settings.php
```

Set the ownership of the drupal directory to the current user and group.

```bash
sudo chown -R www-data:www-data /var/www/html/drupal/
sudo chmod -R 755 /var/www/html/drupal/
```

### Set the Drupal Website as active

Check to see what the active websites are

```bash
ls -alhtr /etc/nginx/sites-enabled
total 8.0K
lrwxrwxrwx 1 root root   34 Aug 19 16:13 default -> /etc/nginx/sites-available/sandbox_fkayiwa
drwxr-xr-x 9 root root 4.0K Aug 23 12:30 ..
```

Remove any current enabled sites with

```bash
sudo rm /etc/nginx/sites-enabled/sandbox_fkayiwa
```

Enable your Drupal Website by linking the config file to the nginx sites-enabled directory.

```bash
sudo ln -s /etc/nginx/sites-available/drupal_sandbox_fkayiwa /etc/nginx/sites-enabled/
```
Restart nginx.

```bash
sudo systemctl restart nginx.service
```
Access the Drupal page through your browser using your IP address. For example:

https://sandbox-fkayiwa.lib.princeton.edu


You have installed Drupal on your server. You can now configure your database credentials.

Use drupal as the database name. Use drupal as the database user name Use the strong password you chose for the database password.
