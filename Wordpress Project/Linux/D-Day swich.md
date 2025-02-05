
## 1  DNS record switch
add 
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
