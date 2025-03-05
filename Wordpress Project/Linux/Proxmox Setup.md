

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

> [!WARNING]  
> disable the firewall ( to prevent locking out ourselves )


``` bash
ufw disable
```

``` bash 
ufw default deny incoming
ufw default allow outgoing
ufw allow in on vmbr1 to any
```
vmbr1 is our internal network that serve the VM's we need to configure it after

``` bash

ufw allow from 1.2.3.4 to any port 22
```


> [!WARNING]  
> Check the rules before enabling the firewall


``` bash
ufw status
```

if everything is correct enable UFW

``` bash 
ufw enable
```

### Creating the internal network vmrb1
System > Network > Create > Linux Bridge
<<< screen from proxmox

### Routing configuration

``` bash
nano /etc/network/interfaces
```

edit the interface

``` bash
nano /etc/network/interface
```


<< insert code block from the proxmox



### Port frowarding to virtual machine

to froward the ports 80/443 to our vm, also add ssh port,

Create a script that will run after the interface come up

``` bash
nano /dnat.sh
```


<<< complete with the actual one
``` bash 
sleep 60
iptables -t nat -A PREROUTING -i vmbr0 -p tcp --dport 80 -j DNAT --to-destination 192.168.100.104:80
iptables -t nat -A PREROUTING -i vmbr0 -p tcp --dport 443 -j DNAT --to-destination 192.168.100.104:443
iptables -t nat -A PREROUTING -i vmbr0 -p tcp --dport 2222 -j DNAT --to-destination 192.168.100.104:22
```