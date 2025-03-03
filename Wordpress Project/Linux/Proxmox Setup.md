

### Setup frowarding and NAT

install ufw
```bash
apt-get install ufw
```

edit the config 
``` bash 
nano /etc/default/ufw
```

add 
```
DEFAULT_FORWARD_POLICY="ACCEPT"
```

edit sysctl

``` bash
nano /etc/ufw/sysctl.conf
```

``` bash
net/ipv4/ip_forward=1
net/ipv6/conf/default/forwarding=1
net/ipv6/conf/all/forwarding=1
```

disable the firewall ( to prevent locking out ourselves )

``` bash
ufw disable
```

``` bash 
ufw default deny incoming
ufw default allow outgoing
ufw allow in on vmbr1 to any
```
vmbr1 is our internal network that serve the VM's we need to configure it after