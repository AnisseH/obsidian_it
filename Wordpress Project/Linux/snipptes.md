


```bash
# run in the terminal, then set as ssl_dhparam in nginx.conf
mkdir /etc/nginx/ssl
openssl dhparam -out /etc/nginx/ssl/dhparam.pem 4096
```


```bash
    set_real_ip_from 173.245.48.0/20;

    set_real_ip_from 103.21.244.0/22;

    set_real_ip_from 103.22.200.0/22;

    set_real_ip_from 103.31.4.0/22;

    set_real_ip_from 141.101.64.0/18;

    set_real_ip_from 108.162.192.0/18;

    set_real_ip_from 190.93.240.0/20;

    set_real_ip_from 188.114.96.0/20;

    set_real_ip_from 197.234.240.0/22;

    set_real_ip_from 198.41.128.0/17;

    set_real_ip_from 162.158.0.0/15;

    set_real_ip_from 104.16.0.0/13;

    set_real_ip_from 104.24.0.0/14;

    set_real_ip_from 172.64.0.0/13;

    set_real_ip_from 131.0.72.0/22;

real_ip_header CF-Connecting-IP;

  
  

define('ADVMO_CLOUDFLARE_R2_KEY', 'a1c6d6eafeb81e347bacda7fb3b3e871');

define('ADVMO_CLOUDFLARE_R2_SECRET', 'ff76218a0351dd9c370de2ca385fdd6a33b37362fd03e4fd613ea76b4a1d4894');

define('ADVMO_CLOUDFLARE_R2_ENDPOINT', 'https://75569de8fbb5761acf8c9ca9cc6c1bde.r2.cloudflarestorage.com');

define('ADVMO_CLOUDFLARE_R2_BUCKET', 'wp-9ahwa-ovh');

define('ADVMO_CLOUDFLARE_R2_DOMAIN', 'media.9ahwa.ovh');

  
  

https://75569de8fbb5761acf8c9ca9cc6c1bde.eu.r2.cloudflarestorage.com
```

old SID
S-1-5-21-1475511947-1274405101-1044252824-45516107