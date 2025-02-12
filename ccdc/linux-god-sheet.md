---
title: Kyle's "Linux God Sheet"
description: Made for CCDC, but widely applicable. Content from here is scattered throughout this wiki.
published: true
date: 2025-02-12T19:22:12.431Z
tags: 
editor: markdown
dateCreated: 2024-10-16T23:04:54.741Z
---

# Kyle's "Linux God Sheet"
Made for CCDC, but widely applicable. Content from here is scattered throughout this wiki.

## Systems
- Check Version
```
lsb_release -a
```
- Ubuntu Wkst: 
	- Release 20.04.6 LTS (focal)
- Ubuntu 18 Web
	- 18.04.6 LTS (bionic)
- CentOS 7 Ecomms
	- 7.1.1503 (Core)
- Fedora 21 Webmail
	- 3.17.4-301.fc21
## OS Hardening 

### Change or LOCK any non-scoring account passwords
```
sudo /usr/bin/passwd
```
> The default `passwd` binary may have been altered to intercept passwords. It is better to explicitly use the `/usr/bin/passwd` binary, or if possible, download binaries from [BusyBox](https://busybox.net/about.html).
{.is-warning}

#### Using the BusyBox passwd binary
```sh
wget https://busybox.net/downloads/binaries/1.35.0-x86_64-linux-musl/busybox_PASSWD
chmod +x ./busybox_PASSWD
sudo ./busybox_PASSWD [regular passwd arguments]
```

### Software Patching

#### Ubuntu
```
sudo apt-get update
sudo apt-get upgrade
```

#### CentOS [Quick Hardening](https://highon.coffee/blog/security-harden-centos-7/#auditd---audit-daemon)
```
sudo yum check-update
sudo yum update
```

#### Fedora
```
sudo yum update
sudo yum upgrade
sudo yum --security update
```

### Implement Auditd
- Follow the auditd doc [here](https://drive.google.com/file/d/1fPzdUayu1mhvMvkypxrburAkM8xK3ED4/view?usp=drive_link)
### Secure User Accounts
1.) Check what Accounts have UID 0 (should be root)
- Lists every account in /etc/passwd that has a UID of 0
```
awk -F: '($3=="0"){print}' /etc/passwd
```
2.) Check for Accounts that have Empty Passwords
- Reads through /etc/shadow and prints every user that has an empty password
```
sudo cat /etc/shadow | awk -F: '($2==""){print $1}'
```
3.) Lock any users with empty passwords
```
sudo passwd -l accountName
```
### Securing SSH
- Config Loc: `/etc/ssh/sshd_config`
1.) Disable Root Login (whatever it is --> no)
```
PermitRootLogin no
```
2.) Suppose we want to allow a user to ssh 
```
AllowUsers <AccountName>
```
3.) Disallow Login w/ Empty Password (By default it is 'no' but you never know)
```
PermitEmptyPasswords no
```
4.) Restart ssh
```
service ssh restart
```
### Remove/Disable Unnecessary Services
- List all services
```
systemctl list-units --type=service
```
- Start/Stop Service
```
sudo service <service> <stop/start>
```
- Enable/Disable Service
```
sudo systemctl <enable/disable> <service>
```
- Check if active/enabled
```
sudo systemctl <is-active/is-enabled> <service>
```
- Show Service
```
sudo systemctl show <service>
```
- Disable Services highlighted in red [here](https://docs.google.com/document/d/1A-MfD2_vfkRYokSO5w78uJjcB1ouHiaJ8sx1dRQV2mo/edit?usp=sharing)
### System Firewall Setup
- Ubuntu 18/CentOS
	- HTTP: 80 TCP
	- HTTPS: 443 TCP
	- Deny the Rest
- Ubuntu Workstation
	- Yes =)
- Fedora
	- SMTP: 25, 587 TCP
	- IMAP: 143, 993 TCP
	- POP3: 110, 995 TCP
#### Implementing UFW 
- References
	- [Basic UFW Configuration](https://www.cherryservers.com/blog/how-to-configure-ubuntu-firewall-with-ufw)
	- [UFW Ubuntu Guide](https://help.ubuntu.com/community/UFW)
- Enable UFW
```
sudo ufw enable
```
- UFW Status Check
```
sudo ufw status verbose
```
```
sudo ufw app list
```
- UFW by default denies incoming and allows outgoing.
- Add UFW rule
```
sudo ufw <allow/deny> <port>/<optional: protocol>
```
- Remove UFW rule
```
sudo ufw delete <allow/deny> <port>/<optional: protocol>
```
- Default Allow/Deny Incoming/Outgoing
```
sudo default <allow/deny> <incoming/outgoing>
```
- UFW Logging Level 
```
- Off: disabled
- Low: logs all blocked packets not matching the default policy, as well as packets matching pre-set rules.
- high: above, but include rate liming
- full: no rate limiting
```
```
sudo logging <level>
```
- Check IPTables Rule Changes
```
sudo iptables -L
```
#### Implementing IPTables
- Avengers Level Threat Terms
	- Incoming --> Input
	- Outgoing --> Output 
- Flags
```
	-A: Append;   -D: Delete
	-I: Insert;   -R: Replace
	-P: policy;   -L: List
	-N: New Chain -X: Delete Chain
	-E: Rename Chain
	-s: Source;   -d: Destination
	-p: Protocol; -t: table
```
- List Tables
```
sudo iptables -L
```
- Adding entries
	- INSERT (-I) goes top of list
	- APPEND (-A) goes bottom
```
sudo iptables <-I/-A> INPUT <options>
```
```
sudo iptables <-I/-A> OUTPUT <others>
```
- Save entries
```
sudo iptables-save > /etc/sysconfig/iptables
```
## Fail2Ban

References:
-  [Guide](https://www.howtogeek.com/675010/how-to-secure-your-linux-computer-with-fail2ban/)
-  [Incremental Bans](https://visei.com/2020/05/incremental-banning-with-fail2ban/)

Installation:
-  Ubuntu
  ```
  sudo apt-get install fail2ban
  ```
-  Fedora
```
  sudo dnf install fail2ban
```
- CentOS
```
  sudo yum install epel-release
  sudo yum install fail2ban
```

Overall Configuration: 
- Create a jail.local file from the default config
```
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
```
- In /etc/fail2ban/jail.local [DEFAULT], [sshd], [Apache] SECTION
```
# Change to the following:
#    - findtime = 5m 
#    - maxretry = 3  

# THIS ONLY WORKS WITH fail2ban version 0.11.x
# Add to [DEFAULT]:
#    - bantime = 60s 
#    - bantime.increment = true
#    - bantime.factor = 1
#    - bantime.multipliers = 5 15 30 60 300 720

# For any version below 0.11.x, ugh... just make bantime 10 minutes
#    - bantime = 10m

# Enable Apache monitoring inside [apache]
#    - enabled = true
```
- Reload Config
```
sudo fail2ban-client reload
```

Enable:
- Start Service
```
# Start Service
sudo systemctl start fail2ban
```
- Enable as a Service
```
sudo systemctl enable fail2ban
```
- Check Service status
```
sudo systemctl status fail2ban.service
```
- Status Update
```
sudo fail2ban-client status
sudo fail2ban-client status sshd
```
- Restart
```
sudo service fail2ban restart
```
Management:
- Unban
```
sudo fail2ban-client set sshd unbanip <IP>
```

Logs
- Location
```
/var/log/fail2ban.log
```
- View Ban Entries
```
awk '($(NF-1) = /Ban/){print "Date: "$1, "Time: "$2, "Offender: "$NF}' /var/log/fail2ban.log | sort -k 1,2
```
- View Unban Entries
```
awk '($(NF-1) = /Unban/){print "Date: "$1, "Time: "$2, "Offender: "$NF}' /var/log/fail2ban.log | sort -k 1,2
```
## Application Security

### Securing Apache2 
#### Guides
- [Installation](https://ubuntu.com/tutorials/install-and-configure-apache#1-overview)
- [How to Harden Your Apache Web Server on an Ubuntu 18.04](https://hostadvice.com/how-to/web-hosting/ubuntu/how-to-harden-your-apache-web-server-on-ubuntu-18-04/)
#### Make Backups 
- Apache2 Config
```
sudo cp /etc/apache2/apache2.conf /etc/apache2/apache2.conf.backup
```
- Security Config
```
sudo cp /etc/apache2/conf-enabled/security.conf /etc/apache2/conf-enabled/security.conf.backup
```
If Ubuntu 18 is on IPv6, then disable IPv6
- IN `/etc/apache2/ports.conf` disable it
```
# From this
Listen 80
<...>
	Listen 443
<...>

<...>
	Listen 443
<...>

# To this
Listen 0.0.0.0:80
<...>
	Listen 0.0.0.0:443
<...>

<...>
	Listen 0.0.0.0:443
<...>
```
#### Hide Apache Version & Ubuntu OS (Even though they probably already know)
- In /etc/apache2/conf-enabled/security.conf, change the following settings:
```
ServerSignature Off
ServerTokens Prod
```
- Reload
```
sudo systemctl reload apache2
```
#### Disable Directory Listing and FollowSymLinks
-  In /etc/apache2/apache2.conf change "Options Indexes FollowSymLinks" to "Options -Indexes -FollowSymLinks" so that it looks like this:
```
<Directory /var/www/>
    Options -Indexes -FollowSymLinks
    AllowOverride None
    Require all granted
</Directory>
```
- Reload
#### Install mod_security and mod_evasive
- Mod Security (Firewall against Brute force protections)
```
sudo apt install libapache2-mod-security2 -y && sudo systemctl restart apache2
```
- Mod Evasive (DDOS/HTTP bruteforce protection)
```
sudo apt install libapache2-mod-evasive -y && sudo systemctl restart apache2
```
#### Limit HTTP Size (If needed)
- Change in /etc/apache2/apache2.conf
- Basically makes the upload cap for 10 mb
```
<Directory "/var/www/html/wp_content/uploads">
 
LimitRequestBody 10485760
 
</Directory>
```
#### Disable TRACE HTTP Request
- Disallows Cross Site Tracing to make it harder to steal cookie info
- In /etc/apache2/apache2.conf, add "TraceEnable off"
```
# Disables Cross-site Tracing
TraceEnable off
```
- Reload

### Securing SMTP
- Dovecat --> POP & IMAP (Defaults already good enough)
	- Config Loc: `/etc/dovecot/dovecot.conf`
- MTA: --> Postfix
	- Config Loc: `/etc/postfix/`
		- `access`: Access Control
		- `main.cf`: Global Config
		- `master.cf`: Controls Interactions w/ other processes
		- `transport`: Email address -> hosts
	- Change to only support Ipv4
	  ```Change: inet_protocols --> ipv4```

### CentOS 
Guides
- [5 Steps](https://saeedrz.medium.com/5-steps-to-hardening-your-server-centos-7-25e1851c8204)
Disable SSH root login
Setup [Firewalld](https://www.redhat.com/sysadmin/beginners-guide-firewalld)
- Default deny by default
- Add/Remove rule by Service
```
sudo firewall-cmd --permanant --zone=external --<add/remove>-service=<service>
```
- Add/Remove rule by port
```
sudo firewall-cmd --permanent --zone=external --<add/remove>-port=<port>/<tcp/udp>
```
- List Config
```
firewall-cmd --zone=external --list-all external
```
- Reload Config
```
sudo firewall-cmd --reload
```
- Enable
```
sudo systemctl enable firewalld
```
Go through the same process as Ubuntu Apache Server Hardening 
- [Guide](https://hostadvice.com/how-to/web-hosting/centos/how-to-harden-the-apache-web-server-on-centos-7/)
- Location for CentOS: `/etc/httpd/conf/httpd.conf`
## Overall References
[Official Ubuntu Hardening Guide](https://ubuntu.com/blog/what-is-system-hardening-definition-and-best-practices)
[Nuharbor Security Ubuntu Hardening Guide](https://www.nuharborsecurity.com/blog/ubuntu-server-hardening-guide-2)
[UFW Ubuntu Guide](https://help.ubuntu.com/community/UFW)