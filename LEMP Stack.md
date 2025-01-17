
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
worker_processes auto; >> worker_processes 2; <<< number o cpu core
...

...
worker_connections 768; >> worker_connections 2048; <<<cpu core x limite file limit
# multi_accept on; << uncomment and set to on
```
