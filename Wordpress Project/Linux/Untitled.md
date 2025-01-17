
```bash
server {

    listen 443 ssl;

    listen [::]:443 ssl;

    http2 on;

  

    server_name wp.9ahwa.ovh;

  

    ssl_certificate /etc/letsencrypt/live/wp.9ahwa.ovh/fullchain.pem;

    ssl_certificate_key /etc/letsencrypt/live/wp.9ahwa.ovh/privkey.pem;

  

    access_log /home/argosadmin/wp.9ahwa.ovh/logs/access.log;

    error_log /home/argosadmin/wp.9ahwa.ovh/logs/error.log;

  

    root /home/argosadmin/wp.9ahwa.ovh/public/;

    index index.php;

  

    location / {

        try_files $uri $uri/ /index.php?$args;

    }

  

    location ~ \.php$ {

        try_files $uri =404;

        fastcgi_split_path_info ^(.+\.php)(/.+)$;

        fastcgi_pass unix:/run/php/php8.3-fpm.sock;

        fastcgi_index index.php;

        include fastcgi.conf;

    }

}

  

server {

    listen 443 ssl;

    listen [::]:443 ssl;

    http2 on;

  

    server_name www.wp.9ahwa.ovh;

  

    ssl_certificate /etc/letsencrypt/live/wp.9ahwa.ovh/fullchain.pem;

    ssl_certificate_key /etc/letsencrypt/live/wp.9ahwa.ovh/privkey.pem;

  

    return 301 https://wp.9ahwa.ovh$request_uri;

}

  

server {

    listen 80;

    listen [::]:80;

  

    server_name wp.9ahwa.ovh www.wp.9ahwa.ovh;

  

    return 301 https://wp.9ahwa.ovh$request_uri;

}
```