
````bash
#!/bin/bash
## By: hellresistor 2k9-2k20
## V20.12.20
## This Script Execute it on first Booting (VPS installing) for Ubuntu 18 20 & Debian 9 10 
## NEW: Prepare your Kali 2020.4 (run as ROOT ...)
## Support with:
## Bitcoin: 13Gr4JiWQBnhCs6AdUNapdfHVu3tG9G6zL
## Bitcanna - B73RRFVtndfPRNSgSQg34yqz4e9eWyKRSv
#################################################################################################################
UsrAdm="argosadmin"                   ## A Admin User 
AutKey="ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIEyTYzegOG8XzqIMI+uvsmvCex9NABCW5WQsuw+Y5kRR argos2003\aha@CZC8317V7Z"   ## Paste Your FULL Public Key generated before (puttygen or ssh-keygen or ...)
# Example: AutKey="ssh-ed25519 AKfifFJs9dCcj9s87g38929ohjxs87125g56S113555422b7 MySpot"
SshGrp="myssh"                       ## A SSH Group dedicated
SSHPORT="22"                         ## Port to SSHD.
ListenIP="0.0.0.0"                   ## 0.0.0.0 = ALL IPs will accept by ssh server
currentscript="$0"
##################################################################################################################
apt install -y lsb-release
if [ -f /etc/os-release ] ; then
source /etc/os-release
else
exit 1
fi
if [ "${ID,,}" = "debian" ] ; then
echo "## $(date) ##
deb http://deb.debian.org/debian ${VERSION_CODENAME,,} main contrib non-free
deb http://deb.debian.org/debian ${VERSION_CODENAME,,}-backports main contrib non-free
deb http://deb.debian.org/debian ${VERSION_CODENAME,,}-updates main contrib non-free
deb http://security.debian.org/debian-security ${VERSION_CODENAME,,}/updates main contrib non-free" > /etc/apt/sources.list
elif [ "${ID,,}" = "ubuntu" ] ; then
echo "## $(date) ##
deb http://archive.ubuntu.com/ubuntu ${VERSION_CODENAME,,} main restricted universe multiverse
deb http://archive.ubuntu.com/ubuntu ${VERSION_CODENAME,,}-security main restricted universe multiverse
deb http://archive.ubuntu.com/ubuntu ${VERSION_CODENAME,,}-updates main restricted universe multiverse
deb http://archive.ubuntu.com/ubuntu ${VERSION_CODENAME,,}-backports main restricted universe multiverse" > /etc/apt/sources.list
elif [ "${ID,,}" = "kali" ] ; then
echo "## $(date) ##
deb http://http.kali.org/kali ${VERSION_CODENAME,,} main contrib non-free" > /etc/apt/sources.list
else
exit 1 
fi
export DEBIAN_FRONTEND=noninteractive
apt-get -y update
echo 'datasource_list: [ None ]' | sudo -s tee /etc/cloud/cloud.cfg.d/90_dpkg.cfg
apt-get purge -y cloud-init && rm -rf /etc/cloud/ && rm -rf /var/lib/cloud/
## apt-get -o Dpkg::Options::="--force-confdef" -o Dpkg::Options::="--force-confold" -y upgrade
apt-get -o Dpkg::Options::="--force-confdef" -o Dpkg::Options::="--force-confold" -y install openssh-server rng-tools iptables
if [ "${ID,,}" = "kali" ] ; then
 echo "HRNGDEVICE=/dev/urandom" >> /etc/default/rng-tools-debian
 touch $HOME/.rnd
 /etc/init.d/rng-tools-debian start
else
 echo "HRNGDEVICE=/dev/urandom" >> /etc/default/rng-tools
 touch $HOME/.rnd
 /etc/init.d/rng-tools start
fi
USERPASS="$(< /dev/urandom tr -cd "[:alnum:]" | head -c 15)" || exit 1
echo -e "${USERPASS}\\n${USERPASS}" | adduser --home /home/"$UsrAdm" --gecos GECOS --force-badname --shell /bin/bash "$UsrAdm"
usermod -aG sudo "$UsrAdm"
echo "$UsrAdm:$USERPASS" > /home/"$UsrAdm"/userinfo.txt
chmod 500 /home/"$UsrAdm"/userinfo.txt
chown "$UsrAdm" /home/"$UsrAdm"/userinfo.txt
echo -e "$UsrAdm\tALL=NOPASSWD:ALL" >> /etc/sudoers
SSH_DIR=/home/"$UsrAdm"/.ssh
mkdir "$SSH_DIR"
echo "$AutKey" >> "$SSH_DIR"/authorized_keys
chown -R "$UsrAdm" "$SSH_DIR"
chgrp -R "$UsrAdm" "$SSH_DIR"
chmod 700 "$SSH_DIR"
chmod 400 "$SSH_DIR"/authorized_keys
chattr +i "$SSH_DIR"/authorized_keys
groupadd "$SshGrp"
usermod -aG "$SshGrp" "$UsrAdm"
cp --preserve /etc/ssh/sshd_config /etc/ssh/sshd_config."$(date +"%Y%m%d%H%M%S")"
sed -i -r -e '/^#|^$/ d' /etc/ssh/sshd_config
cat > /etc/ssh/sshd_config <<-EOTF
Protocol 2
Port $SSHPORT
ListenAddress $ListenIP
HostKey /etc/ssh/ssh_host_ed25519_key
HostKey /etc/ssh/ssh_host_rsa_key
HostKey /etc/ssh/ssh_host_dsa_key
HostKey /etc/ssh/ssh_host_ecdsa_key
AllowGroups $SshGrp
AllowUsers $UsrAdm
SyslogFacility AUTH
LogLevel INFO
PermitRootLogin no
PermitEmptyPasswords no
ClientAliveCountMax 0
ClientAliveInterval 300
LoginGraceTime 30
Compression delayed
StrictModes yes
PubkeyAuthentication yes
PasswordAuthentication no
AuthenticationMethods publickey
AuthorizedKeysFile    %h/.ssh/authorized_keys
Ciphers chacha20-poly1305@openssh.com,aes256-gcm@openssh.com,aes128-gcm@openssh.com,aes256-ctr,aes192-ctr,aes128-ctr
MACs hmac-sha2-512-etm@openssh.com,hmac-sha2-256-etm@openssh.com,umac-128-etm@openssh.com,hmac-sha2-512,hmac-sha2-256,umac-128@openssh.com
KexAlgorithms curve25519-sha256@libssh.org,ecdh-sha2-nistp521,ecdh-sha2-nistp384,ecdh-sha2-nistp256,diffie-hellman-group-exchange-sha256
IgnoreRhosts yes
HostbasedAuthentication no
ChallengeResponseAuthentication no
KerberosAuthentication no
GSSAPIAuthentication no
UsePAM no
X11Forwarding no
PrintMotd no
TCPKeepAlive no
AcceptEnv LANGUAGE
PermitUserEnvironment no
AllowTcpForwarding no
AllowStreamLocalForwarding no
GatewayPorts no
PermitTunnel no
UseDNS no
Compression no
AllowAgentForwarding no
MaxAuthTries 2
MaxSessions 2
MaxStartups 2
DebianBanner no
ChallengeResponseAuthentication no
EOTF
chown root:root /etc/ssh/sshd_config
chmod og-rwx /etc/ssh/sshd_config
awk '$5 >= 3072' /etc/ssh/moduli | tee /etc/ssh/moduli.tmp
mv /etc/ssh/moduli.tmp /etc/ssh/moduli
systemctl restart ssh
echo 1 > /proc/sys/net/ipv4/conf/all/secure_redirects
echo 1 > /proc/sys/net/ipv4/conf/default/secure_redirects
echo 0 > /proc/sys/net/ipv4/conf/all/accept_redirects
echo 0 > /proc/sys/net/ipv4/conf/default/accept_redirects
echo 0 > /proc/sys/net/ipv4/conf/all/send_redirects
echo 0 > /proc/sys/net/ipv4/conf/default/send_redirects
echo 1 > /proc/sys/net/ipv4/icmp_echo_ignore_broadcasts
echo 1 > /proc/sys/net/ipv4/icmp_ignore_bogus_error_responses
echo 0 > /proc/sys/net/ipv4/icmp_echo_ignore_all
echo 0 > /proc/sys/net/ipv4/conf/all/accept_source_route
echo 0 > /proc/sys/net/ipv4/conf/default/accept_source_route
echo 0 > /proc/sys/net/ipv4/conf/all/proxy_arp
echo 0 > /proc/sys/net/ipv4/conf/default/proxy_arp
echo 1 > /proc/sys/net/ipv4/tcp_syncookies
echo 2048 > /proc/sys/net/ipv4/tcp_max_syn_backlog
echo 5 > /proc/sys/net/ipv4/tcp_syn_retries
echo 5 > /proc/sys/net/ipv4/tcp_synack_retries
echo 1 > /proc/sys/net/ipv4/conf/all/rp_filter
echo 1 > /proc/sys/net/ipv4/conf/default/rp_filter
echo 0 > /proc/sys/net/ipv4/conf/all/bootp_relay
echo 0 > /proc/sys/net/ipv4/conf/default/bootp_relay
echo 0 > /proc/sys/net/ipv4/conf/all/log_martians
echo 0 > /proc/sys/net/ipv4/conf/default/log_martians
echo 0 > /proc/sys/net/ipv4/conf/all/accept_source_route
echo 0 > /proc/sys/net/ipv4/conf/default/accept_source_route
echo 1 > /proc/sys/net/ipv4/tcp_sack
echo 1 > /proc/sys/net/ipv4/tcp_dsack
echo 1 > /proc/sys/net/ipv4/tcp_fack
echo 0 > /proc/sys/net/ipv6/conf/all/forwarding
echo 0 > /proc/sys/net/ipv6/conf/default/forwarding
echo 1 > /proc/sys/net/ipv6/conf/all/disable_ipv6
echo 1 > /proc/sys/net/ipv6/conf/default/disable_ipv6
echo 0 > /proc/sys/net/ipv4/ip_forward
echo 0 > /proc/sys/net/ipv4/conf/all/forwarding
echo 0 > /proc/sys/net/ipv4/conf/default/forwarding
cat > /usr/local/sbin/my-firewall.sh <<-EOTF
#!/bin/bash
iptables -F
iptables -X
iptables -t nat -F
iptables -t nat -X
iptables -t mangle -F
iptables -t mangle -X
iptables -t raw -F
iptables -t raw -X
iptables -P INPUT DROP
iptables -P OUTPUT DROP
iptables -P FORWARD DROP
ip6tables -P INPUT DROP
ip6tables -P OUTPUT DROP
ip6tables -P FORWARD DROP
iptables -N SSHBRUTEFORCE
iptables -A SSHBRUTEFORCE -m limit --limit 60/min -j LOG --log-prefix "SSHBruteForce  " --log-level 4
iptables -A SSHBRUTEFORCE -j DROP
iptables -N portscan_drop
iptables -A portscan_drop -m limit --limit 60/m -j LOG --log-prefix "PORTSCAN DETECTED "
iptables -A portscan_drop -j DROP
iptables -N invalid_drop
iptables -A invalid_drop -m conntrack --ctstate INVALID -j LOG --log-prefix "DROP INVALID " --log-ip-options --log-tcp-options 
iptables -A invalid_drop -m conntrack --ctstate INVALID -j DROP
iptables -N accept_pack
iptables -A accept_pack -j LOG --log-prefix "ACCEPT "
iptables -A accept_pack -j ACCEPT
iptables -A INPUT -m conntrack --ctstate INVALID -j invalid_drop
iptables -A OUTPUT -m conntrack --ctstate INVALID -j invalid_drop
iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j accept_pack
iptables -A OUTPUT -m conntrack --ctstate ESTABLISHED,RELATED -j accept_pack
iptables -t mangle -A PREROUTING -p icmp -j LOG --log-prefix "DROP ICMP " --log-ip-options --log-tcp-options
iptables -t mangle -A PREROUTING -p icmp -j DROP
iptables -t mangle -A PREROUTING -m pkttype --pkt-type broadcast -j portscan_drop
iptables -t mangle -A PREROUTING -m pkttype --pkt-type multicast -j portscan_drop
iptables -t mangle -A PREROUTING -p tcp -m tcp -m conntrack --ctstate NEW -m tcpmss ! --mss 536:65535 -j DROP
iptables -t mangle -A PREROUTING -p tcp -m tcp --tcp-flags ALL NONE -j portscan_drop
iptables -t mangle -A PREROUTING -p tcp -m tcp --tcp-flags ALL FIN -j portscan_drop
iptables -t mangle -A PREROUTING -p tcp -m tcp --tcp-flags SYN,FIN SYN,FIN -j portscan_drop
iptables -t mangle -A PREROUTING -p tcp -m tcp --tcp-flags SYN,RST SYN,RST -j portscan_drop
iptables -t mangle -A PREROUTING -p tcp -m tcp --tcp-flags FIN,RST FIN,RST -j portscan_drop
iptables -t mangle -A PREROUTING -p tcp -m tcp --tcp-flags ALL FIN,URG,PSH -j portscan_drop
iptables -t mangle -A PREROUTING -p tcp -m tcp --tcp-flags ALL URG,ACK,PSH,RST,SYN,FIN -j portscan_drop
iptables -t mangle -A PREROUTING -p tcp -m tcp --tcp-flags ALL ALL -j portscan_drop
iptables -t mangle -A PREROUTING -p tcp -m tcp --tcp-flags ACK,FIN FIN -j portscan_drop
iptables -t mangle -A PREROUTING -p tcp -m tcp --tcp-flags ACK,PSH PSH -j portscan_drop
iptables -t mangle -A PREROUTING -p tcp -m tcp --tcp-flags ACK,URG URG -j portscan_drop
iptables -t mangle -A PREROUTING -p tcp -m tcp --tcp-flags FIN,ACK FIN -j portscan_drop
iptables -t mangle -A PREROUTING -p tcp -m tcp --tcp-flags FIN,SYN,RST,PSH,ACK,URG NONE -j portscan_drop
iptables -t mangle -A PREROUTING -p tcp -m tcp --tcp-flags FIN,SYN FIN,SYN -j portscan_drop
iptables -t mangle -A PREROUTING -p tcp -m tcp --tcp-flags FIN,ACK FIN -j portscan_drop
iptables -t mangle -A PREROUTING -p tcp -m tcp --tcp-flags ALL FIN,PSH,URG -j portscan_drop
iptables -t mangle -A PREROUTING -p tcp -m tcp --tcp-flags ALL SYN,FIN,PSH,URG -j portscan_drop
iptables -t mangle -A PREROUTING -p tcp -m tcp --tcp-flags ALL SYN,RST,ACK,FIN,URG -j portscan_drop
iptables -t mangle -A PREROUTING -p tcp -m tcp --tcp-flags PSH,ACK PSH -j portscan_drop
iptables -t mangle -A PREROUTING -p tcp -m tcp --tcp-flags FIN,SYN,RST,PSH,ACK,URG FIN,SYN,RST,PSH,ACK,URG -j portscan_drop
iptables -t mangle -A PREROUTING -p tcp -m tcp --tcp-flags FIN,SYN,RST,PSH,ACK,URG FIN,PSH,URG -j portscan_drop
iptables -t mangle -A PREROUTING -p tcp -m tcp --tcp-flags FIN,SYN,RST,PSH,ACK,URG FIN,SYN,PSH,URG -j portscan_drop
iptables -t mangle -A PREROUTING -p tcp -m tcp --tcp-flags FIN,SYN,RST,PSH,ACK,URG FIN,SYN,RST,ACK,URG -j portscan_drop
ip6tables -A INPUT -i lo -j accept_pack
ip6tables -A OUTPUT -o lo -j accept_pack
iptables -A INPUT -p tcp --dport $SSHPORT -m conntrack --ctstate NEW -m recent --set
iptables -A INPUT -p tcp --dport $SSHPORT -m conntrack --ctstate NEW -m recent --update --seconds 60 --hitcount 6 -j SSHBRUTEFORCE
iptables -A INPUT -p tcp --dport $SSHPORT -m conntrack --ctstate NEW -j accept_pack 
iptables -A OUTPUT -p tcp --dport $SSHPORT -m conntrack --ctstate NEW -j accept_pack
iptables -A OUTPUT -p tcp --dport 80 -m conntrack --ctstate NEW -j accept_pack
iptables -A OUTPUT -p tcp --dport 443 -m conntrack --ctstate NEW -j accept_pack
iptables -A INPUT -p udp --dport 53 -m conntrack --ctstate NEW -j accept_pack
iptables -A OUTPUT -p tcp --dport 53 -m conntrack --ctstate NEW -j accept_pack
iptables -A OUTPUT -p udp --dport 53 -m conntrack --ctstate NEW -j accept_pack
iptables -A INPUT -j LOG --log-prefix "DROP INPUT " --log-ip-options --log-tcp-options
iptables -A INPUT -j DROP
iptables -A OUTPUT -j LOG --log-prefix "DROP OUTPUT " --log-ip-options --log-tcp-options
iptables -A OUTPUT -j DROP
iptables -A FORWARD -j LOG --log-prefix "DROP FORWARD " --log-ip-options --log-tcp-options
iptables -A FORWARD -j DROP
EOTF
chmod 744 /usr/local/sbin/my-firewall.sh
cat > /etc/systemd/system/my-firewall.service  <<-EOTF
[Unit]
After=network.target

[Service]
ExecStart=/usr/local/sbin/my-firewall.sh

[Install]
WantedBy=default.target
EOTF
chmod 664 /etc/systemd/system/my-firewall.service
systemctl daemon-reload
systemctl enable my-firewall
function finish {
echo "Securely shredding ${currentscript}"; shred -u "${currentscript}"; shred -u /root/init.script; shred -u /root/init.script.log; reboot;
}
trap finish EXIT
```