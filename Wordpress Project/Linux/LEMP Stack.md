
## Nginx


```bash
sudo add-apt-repository ppa:ondrej/nginx -y
sudo apt update
```

getting cpu core numbers

```bash
grep processor /proc/cpuinfo | wc -l
```

getting file limit
```bash
ulimit -n
```


edit nginx conf file 
```bash
sudoedit /etc/nginx/nginx.conf
```

```
...
user argosadmin; < set to the correct user/group
worker_processes 2; <<< number o cpu core
...

...
worker_connections 2048; <<<cpu core x limite file limit
multi_accept on; << uncomment and set to on
...

...
keepalive_timeout 15s; <<< add to kill connection after 15 secondes
server_tokens off; << uncomment and set to off
client_max_body_size 64m;
...

...
gzip_proxied any;
gzip_comp_level 5; << set level of compression 1-9, higer value impact cpu usage
gzip_types text/plai....  <<< uncomment 
...

```


## Install PHP 8.3

```bash
sudo add-apt-repository ppa:ondrej/php -y
sudo apt update
```

```bash
sudo apt install php8.3-fpm php8.3-common php8.3-mysql \
php8.3-xml php8.3-intl php8.3-curl php8.3-gd \
php8.3-imagick php8.3-cli php8.3-dev php8.3-imap \
php8.3-mbstring php8.3-opcache php8.3-redis \
php8.3-soap php8.3-zip -y
```


```bash
sudo nano /etc/php/8.3/fpm/pool.d/www.conf
```

```
...
user = argosadmin < set to the correct user/group
group = argosadmin < set to the correct user/group
...

...
listen.owner = argosadmin < set to the correct user/group
listen.group = argosadmin < set to the correct user/group
...
```


```bash
sudo nano /etc/php/8.3/fpm/php.ini
```

```
...
upload_max_filesize = 64M < set 64m
...

...
post_max_size = 64M < set 64m
...

...
opcache.enable_file_override = 1 < uncomment and set to 1 to enable opcache
...
```


## Install MySQL

```bash
sudo apt install mysql-server -y
```


```bash
sudo mysql
```
setup a temporary password to run the secure instalation
```bash
ALTER USER 'root'@'localhost' IDENTIFIED WITH caching_sha2_password BY 'password';
```

```bash
exit
```

```bash
sudo mysql_secure_installation
```


## HTTPS

sudo certbot --nginx certonly -d wp.9ahwa.ovh -d www.wp.9ahwa.ovh

Certificate is saved at: /etc/letsencrypt/live/wp.9ahwa.ovh/fullchain.pem
Key is saved at:         /etc/letsencrypt/live/wp.9ahwa.ovh/privkey.pem

```bash
sudo ln -s /etc/nginx/sites-available/wp.9ahwa.ovh /etc/nginx/sites-enabled/wp.9ahwa.ovh
```


## WP site

```mysql
CREATE USER 'argosadmin'@'localhost' IDENTIFIED BY 'edE9KLbt&%7Wcb';
```

```mysql
CREATE DATABASE wp CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_520_ci;
```

```mysql
GRANT ALL PRIVILEGES ON wp.* TO 'argosadmin'@'localhost';
```

```bash
wp core config --dbname=wp --dbuser=argosadmin --dbpass='edE9KLbt&%7Wcb'
```

```bash
wp core install --skip-email --url=https://wp.9ahwa.ovh --title='Argos' --admin_user=aha --admin_email=aha@argos.fund --admin_password='V8g8XP!Nv#u&hx'
```

```bash
wp search-replace 'https://argos.wityu.fund' 'https://wp.9ahwa.ovh' --allow-root --dry-run
```