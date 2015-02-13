#### Set-Up UFW (Uncomplicated Firewall)

**Install Firewall if it's not already installed**

```
sudo apt-get update
sudo apt-get install ufw
```

**Set Deny as the default rule (this blocks everything)**

`sudo ufw default deny`

**Allow SSH with new port 22233 [update ssh port prior to this](https://github.com/respondiv/Set-up-Linux-Server/blob/master/6-Add-Edit-Users.md#change-the-ssh-port-for-security)**

`sudo ufw allow 22233/tcp`

**Allow HTTP**

`sudo ufw allow 80/tcp`

**Allow HTTPS**

`sudo ufw allow 443/tcp`

**Allow MySQL**

`sudo ufw allow 3306/tcp`

**Allow web admin access port for EasyEngine, default 22222 [update port](https://github.com/respondiv/Set-up-Linux-Server/blob/master/3-Install-Easy-Engine.md#lets-secure-easyengine-while-we-are-at-it)**

`sudo ufw allow 33344/tcp`

**Enable the firewall**

`sudo ufw enable`

**Check firewall status**

`sudo ufw status verbose`

#### Secure Shared Memory

To prevent shared memory being used against a service attack, update `/etc/fstab` with following

```
# Open file in File Editor
sudo nano /etc/fstab

#Add this line to the end of file
tmpfs /dev/shm tmpfs defaults,noexec,nosuid 0 0
```

Press `ctrl+x` and type `y` to save and exit

#### Network Hardening & Optimizing

We need to edit `/etc/sysctl.conf` in order to prevent incorrect packet routing and malformed IP’s.

`sudo nano /etc/sysctl.conf`

Add following lines at the end of file

```
# Recycle Zombie connections
net.inet.tcp.fast_finwait2_recycle=1
net.inet.tcp.maxtcptw=200000

# Increase number of files
kern.maxfiles=65535
kern.maxfilesperproc=16384

# Increase page share factor per process
vm.pmap.pv_entry_max=54272521
vm.pmap.shpgperproc=20000

# Increase number of connections
vfs.vmiodirenable=1
kern.ipc.somaxconn=3240000
net.inet.tcp.rfc1323=1
net.inet.tcp.delayed_ack=0
net.inet.tcp.restrict_rst=1
kern.ipc.maxsockbuf=2097152
kern.ipc.shmmax=268435456

# Host cache
net.inet.tcp.hostcache.hashsize=4096
net.inet.tcp.hostcache.cachelimit=131072
net.inet.tcp.hostcache.bucketlimit=120

# Increase number of ports
net.inet.ip.portrange.first=2000
net.inet.ip.portrange.last=100000
net.inet.ip.portrange.hifirst=2000
net.inet.ip.portrange.hilast=100000
kern.ipc.semvmx=131068

# Disable Ping-flood attacks
net.inet.tcp.msl=2000
net.inet.icmp.bmcastecho=1
net.inet.icmp.icmplim=1
net.inet.tcp.blackhole=2
net.inet.udp.blackhole=1

# Add/Edit the following lines to the file
# IP Spoofing protection
net.ipv4.conf.all.rp_filter = 1
net.ipv4.conf.default.rp_filter = 1
 
# Ignore ICMP broadcast requests
net.ipv4.icmp_echo_ignore_broadcasts = 1
 
# Disable source packet routing
net.ipv4.conf.all.accept_source_route = 0
net.ipv6.conf.all.accept_source_route = 0
net.ipv4.conf.default.accept_source_route = 0
net.ipv6.conf.default.accept_source_route = 0
 
# Ignore send redirects
net.ipv4.conf.all.send_redirects = 0
net.ipv4.conf.default.send_redirects = 0
 
# Block SYN attacks
net.ipv4.tcp_syncookies = 1
net.ipv4.tcp_max_syn_backlog = 2048
net.ipv4.tcp_synack_retries = 2
net.ipv4.tcp_syn_retries = 5
 
# Log Martians
net.ipv4.conf.all.log_martians = 1
net.ipv4.icmp_ignore_bogus_error_responses = 1
 
# Ignore ICMP redirects
net.ipv4.conf.all.accept_redirects = 0
net.ipv6.conf.all.accept_redirects = 0
net.ipv4.conf.default.accept_redirects = 0
net.ipv6.conf.default.accept_redirects = 0
 
# Ignore Directed pings
net.ipv4.icmp_echo_ignore_all = 1

```

Press `ctrl+x` and type `y` to save and exit

**Reload sysctl**

`sudo sysctl -p`

#### Prevent IP Spoofing

Edit the hosts config

`sudo nano /etc/host.conf`

Add / Edit following lines

```
# only add this if its not present
order bind,hosts

# add this at the end of file
nospoof on
```

#### Ban Hosts - Using Fail2Ban

**Install Fail2Ban**

```
sudo apt-get update
sudo apt-get install fail2ban
```

**Install sendmail to get notification and install iptables-persistent to allow the server to automatically setup our firewall rules at boot**

`sudo apt-get install sendmail iptables-persistent`

**To Edit Faile2Ban config, copy the config file (jail.conf) to jail.local to prevent it from modifying when upgrading**

`sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local`

Establish a base firewall. With ufw setup no need for this, but you can view these settings on : [https://www.digitalocean.com/community/tutorials/how-to-install-and-use-fail2ban-on-ubuntu-14-04](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-fail2ban-on-ubuntu-14-04)

**Edit Fail2Ban conf file**

`sudo nano /etc/fail2ban/jail.local`

**Add / Edit the necessary config and Enable needed services**

```
# ignore IP, you can ignore multiple ip by separating them with spaces
ignoreip = 127.0.0.1/8 1

# ban time - in seconds
bantime = 3600

# ban a client that unsuccessfully attempts to log in 5 times within a 600 secs (10
minute) window.
findtime = 600
maxretry = 5

# setup to email address to get an alert email
destemail = webmaster@example.com

# choose the default action by changing the line "action = %(action_)s" to:
action = %(action_mwl)s

# default protocol tcp
protocol = all

# SSH - be sure to update SSH port
[ssh]
enabled = true
# port = ssh # comment this line if the port number has been changed from 22
port = 22233 # enter new port number
filter = sshd
logpath = /var/log/auth.log
maxretry = 3
bantime = 3600

# SSH - DDOS
[ssh-ddos]
enabled = true
# port = ssh # comment this line if the port number has been changed from 22
port = 22233 # enter new port number
filter = sshd-ddos
logpath = /var/log/auth.log
maxretry = 6
bantime = 3600

# protect the password enabled portion of the website from getting slammed with login attempts.
# this needs setup https://www.howtoforge.com/basic-http-authentication-with-nginx
# or https://www.digitalocean.com/community/tutorials/how-to-set-up-httpauthentication-
with-nginx-on-ubuntu-12-10

[nginx-http-auth]
enabled = true
filter = nginx-http-auth
port = http,https
logpath = /var/log/nginx*/*error*.log
maxretry = 6
bantime = 3600

# prevent bruteforce by limiting php throttling request
[nginx-ratelimit]
enabled = true
port = http,https
filter = nginx-ratelimit
logpath = /var/log/nginx*/*error*.log
maxretry = 10
bantime = 3600

# Blocks IPs that fail to authenticate using web application's log in page
[nginx-login]
enabled = true
filter = nginx-login
port = http,https
logpath = /var/log/nginx*/*access*.log
bantime = 3600
maxretry = 6

# Block IPs trying to execute scripts such as .php, .pl, .exe and other funny scripts.
[nginx-noscript]
enabled = true
port = http,https
filter = nginx-noscript
logpath = /var/log/nginx*/*access*.log
maxretry = 6
bantime = 86400

# Block IPs trying to use server as proxy
[nginx-proxy]
enabled = true
port = http,https
filter = nginx-proxy
logpath = /var/log/nginx*/*access*.log
maxretry = 0
bantime = 86400

# block bad bots
[nginx-badbots]
enabled = true
filter = apache-badbots
port = http,https
logpath = /var/log/nginx*/*access*.log
bantime = 86400
maxretry = 1
```

Press `ctrl+x` and type `y` to save and exit

**Now we need to create filter(s) / file(s) in `/etc/fail2ban/filder.d`**

Change Directory

`cd /etc/fail2ban/filter.d`

**Filter for Preventing brute force by php throttling limit**

`sudo nano nginx-ratelimit.conf`

Copy paste following

```
# filter for php throttling limiting request to prevent brute force
[Definition]
failregex = ^.* limiting requests, excess: .*, client: <HOST>, .*$
ignoreregex =
```

Press `ctrl+x` and type `y` to save and exit

**Filter for Blocking IPs that fail to authenticate using web application's log in page**

`sudo nano nginx-login.conf`

Copy paste following

```
# Blocks IPs that fail to authenticate using web application's log in page
# Scan access log for HTTP 200 + POST /sessions => failed log in

[INCLUDES]

# Read common prefixes. If any customizations available -- read them from common.local

before = common.conf
[Definition]
failregex = ^<HOST> -.*POST /sessions HTTP/1\.." 200
ignoreregex =
```

Press `ctrl+x` and type `y` to save and exit

**Filter for Blocking IPs trying to execute scripts such as .php, .pl, .exe and other funny scripts**

`sudo nano nginx-noscript.conf`

Copy paste following

```
# Block IPs trying to execute scripts such as .php, .pl, .exe and other funny scripts.
# Matches e.g.
# 192.168.1.1 - - "GET /something.php

[INCLUDES]

# Read common prefixes. If any customizations available -- read them from common.local

before = common.conf
[Definition]
failregex = ^<HOST> -.*GET.*(\.php|\.asp|\.exe|\.pl|\.cgi|\scgi)
ignoreregex =
```

Press `ctrl+x` and type `y` to save and exit

**Filter for Blocking IPs trying to use server as proxy**

`sudo nano nginx-proxy.conf`

Copy paste following

```
# Block IPs trying to use server as proxy.
# Matches e.g.
# 192.168.1.1 - - "GET http://www.something.com/

[INCLUDES]

# Read common prefixes. If any customizations available -- read them from common.local

before = common.conf
[Definition]
failregex = ^<HOST> -.*GET http.*
ignoreregex =
```

Press `ctrl+x` and type `y` to save and exit

**Shutdown Fail2Ban service**

`sudo service fail2ban stop`

**Start the service**

`sudo service fail2ban start`

**Check status**

```
# displays all status
sudo fail2ban-client status

# for each jail status, e.g SSH
sudo fail2ban-client status ssh
```

**Unban single ip, get jail name from above status command**

```
# Syntax: fail2ban-client get jail-name actionunban ipaddress
fail2ban-client get ssh actionunban 1.2.3.4
```

#### Reboot is required

`sudo reboot now`


