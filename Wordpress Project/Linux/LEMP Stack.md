
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
user = argosadmin
group = argosadmin
...

...
listen.owner = argosadmin
listen.group = argosadmin
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