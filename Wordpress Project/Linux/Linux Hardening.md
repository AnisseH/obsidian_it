https://github.com/imthenachoman/How-To-Secure-A-Linux-Server


## Pour la partie SSH
[Stargazers · SSHEnforcer.sh](https://gist.github.com/hellresistor/108d965b32a907558ed101e9a0f4326c/stargazers)

## Pour le ntp
```bash
sudo apt install systemd-timesyncd
```

```bash
sudo timedatectl set-timezone Europe/Paris
```

```bash
sudoedit /etc/systemd/timesyncd.conf
```

```bash
NTP=pool.ntp.org
```

```bash
sudo timedatectl set-ntp on
```


## hidepid=2
> [!NOTE]  
> Skip> probleme avec systemd.

echo "This email body is sent with Mailtrap" | mail -s "Mailtrap Linux email" recipient@example.com

## separate iptables

> [!WARNING]  
> bien repointer PSAD et crowdsec dessus



## Test ports
> [!NOTE]  
> Commande pour tester les ports.

```bash
sudo nmap -Pn --top-ports 1000 portquiz.net
```

