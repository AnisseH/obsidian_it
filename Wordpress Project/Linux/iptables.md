````bash
 sudo iptables -P INPUT DROP

 sudo iptables -P FORWARD DROP

 sudo iptables -P OUTPUT DROP

 sudo iptables -N CROWDSEC_CHAIN

 sudo iptables -N SSHBRUTEFORCE

 sudo iptables -N accept_pack

 sudo iptables -N invalid_drop

 sudo iptables -N portscan_drop

 sudo iptables -A INPUT -j CROWDSEC_CHAIN

 sudo iptables -A INPUT -m conntrack --ctstate INVALID -j invalid_drop

 sudo iptables -A INPUT -m conntrack --ctstate RELATED,ESTABLISHED -j accept_pack

 sudo iptables -A INPUT -p tcp -m tcp --dport 22 -m conntrack --ctstate NEW -m recent --set --name DEFAULT --mask 255.255.255.255 --rsource

 sudo iptables -A INPUT -p tcp -m tcp --dport 22 -m conntrack --ctstate NEW -m recent --update --seconds 60 --hitcount 6 --name DEFAULT --mask 255.255.255.255 --rsource -j SSHBRUTEFORCE

 sudo iptables -A INPUT -p tcp -m tcp --dport 22 -m conntrack --ctstate NEW -j accept_pack

 sudo iptables -A INPUT -p udp -m udp --dport 53 -m conntrack --ctstate NEW -j accept_pack

 sudo iptables -A INPUT -j LOG --log-prefix "DROP INPUT " --log-tcp-options --log-ip-options

 sudo iptables -A INPUT -j DROP

 sudo iptables -A INPUT -i lo -j ACCEPT

 sudo iptables -A FORWARD -j LOG --log-prefix "DROP FORWARD " --log-tcp-options --log-ip-options

 sudo iptables -A FORWARD -j DROP

 sudo iptables -A OUTPUT -m conntrack --ctstate INVALID -j invalid_drop

 sudo iptables -A OUTPUT -m conntrack --ctstate RELATED,ESTABLISHED -j accept_pack

 sudo iptables -A OUTPUT -p tcp -m tcp --dport 22 -m conntrack --ctstate NEW -j accept_pack

 sudo iptables -A OUTPUT -p tcp -m tcp --dport 80 -m conntrack --ctstate NEW -j accept_pack

 sudo iptables -A OUTPUT -p tcp -m tcp --dport 443 -m conntrack --ctstate NEW -j accept_pack

 sudo iptables -A OUTPUT -p tcp -m tcp --dport 587 -m conntrack --ctstate NEW -j accept_pack

 sudo iptables -A OUTPUT -p tcp -m tcp --dport 25 -m conntrack --ctstate NEW -j accept_pack

 sudo iptables -A OUTPUT -p tcp -m tcp --dport 53 -m conntrack --ctstate NEW -j accept_pack

 sudo iptables -A OUTPUT -p udp -m udp --dport 53 -m conntrack --ctstate NEW -j accept_pack

 sudo iptables -A OUTPUT -j LOG --log-prefix "DROP OUTPUT " --log-tcp-options --log-ip-options

 sudo iptables -A OUTPUT -j DROP

 sudo iptables -A OUTPUT -o lo -j ACCEPT

 sudo iptables -A SSHBRUTEFORCE -m limit --limit 1/sec -j LOG --log-prefix "SSHBruteForce  "

 sudo iptables -A SSHBRUTEFORCE -j DROP

 sudo iptables -A accept_pack -j LOG --log-prefix "ACCEPT "

 sudo iptables -A accept_pack -j ACCEPT

 sudo iptables -A invalid_drop -m conntrack --ctstate INVALID -j LOG --log-prefix "DROP INVALID " --log-tcp-options --log-ip-options

 sudo iptables -A invalid_drop -m conntrack --ctstate INVALID -j DROP

 sudo iptables -A portscan_drop -m limit --limit 1/sec -j LOG --log-prefix "PORTSCAN DETECTED "
```