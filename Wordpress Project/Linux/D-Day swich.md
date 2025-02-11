- [x] 1-move dns to cloudflare ✅ 2025-02-07
- [ ] 2-before the last backup disable wp_fastest cache, advanced cache and object cache
- [ ] download backup of 16/02/2024 and restore it on the new host (don't forget to remove the wpe plugins in mu-plugins folder and entries in the wp-config.conf )
- [ ] enable the route for the maintenance page
- [ ] change the dns entries to the new host with the new one and enable cloudflare proxy
- [ ] test and debug the new website without any new plugin
- [ ] add Redis plugins and configure it ( cache mysql requests )
- [ ] configure cloudflare for wordpresse for app optimisation
- [ ] configure crowdstrike for wordpress ( retest appsec )
- [ ] remove noindex header
- [ ] Disable maintenance page route
## 1  DNS record switch
add route to the maintenance page
[Cloudflare: How to show a maintenance page but continue traffic to origin server from whitelisted IPs](https://www.infiniroot.com/blog/1226/cloudflare-how-to-show-maintenance-page-ip-exception-whitelist)
add ilidiosd@gmail.com ip ?

## 2 - generate certificate
```bash 
sudo certbot --nginx certonly -d argos.wityu.fund -d www.argos.wityu.fund
```

```bash
sudo nano /etc/nginx/sites-available/argos.wityu.fund
```

put the correct path for the let's encrypt certificate 

```
        # Paths to certificate files.
        ssl_certificate /home/argosadmin/ssl/certificate-argos.wityu.fund.crt;
        ssl_certificate_key /home/argosadmin/ssl/certificate-argos.wityu.fund.key;
```

to 
```
ssl_certificate /etc/letsencrypt/live/argos.wityu.fund/fullchain.pem; ssl_certificate_key /etc/letsencrypt/live/argos.wityu.fund/privkey.pem;
```



## X Remove robots x header 

delete this line in 
```bash 
sudoedit /etc/nginx/sites-available/argos.wityu.fund
```

```
        # Disable robots indexing
        add_header  X-Robots-Tag "noindex, nofollow, nosnippet, noarchive";
```