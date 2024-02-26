---
title: Hardening
description: 
published: true
date: 2024-02-26T02:36:38.053Z
tags: 
editor: markdown
dateCreated: 2024-02-22T06:11:58.378Z
---

# General Hardening
There's a few mindsets to use when hardening. Easiest is the *prevent all unauthorized access* mindset, which is great to quickly prevent initial access and to limit attacker capabilities. That comes down to just a few things:
1. Change credentials and lock/remove accounts ([guide](os-info.md))
2. Disable services ([guide](services.md))
3. Ensure nothing is reaching out that shouldn't be (more info [here](incident-response.md))
	1. Check crontabs for network connections
	2. Check important system files for backdoors ([guide](os-info.md))
4. Enable firewall (explained below)
5. Secure services (explained below)

The next mindset is realizing that an attacker **will** get into the system as long as a service is up, so you have to prevent privilege escalation. While this list is shorter, it's also a little more complicated:
1. Check file and executable permissions, including SUID/SGID ([guide](os-info.md))
2. Check important system files, like `/etc/passwd`, `/etc/sudoers`, etc, for misconfigurations ([guide](os-info.md))
3. Check for privilege escalation vectors, like `env`, plaintext passwords, or suspicious files. This also includes making important files immutable ([guide](os-info.md))
4. Backup important files/directories

Note that having multiple eyes and rotation on machines may help find new vulnerabilities, but sticking to a machine may build familiarity.

Remember to set up logging after any hardening! ([guide](logging.md))
# Service Hardening
## SSH
SSH configuration isn't *too* difficult. [Here](https://help.ubuntu.com/community/SSH/OpenSSH/Configuring) is a guide to setting up the config file (`/etc/ssh/sshd_config`)

And here is a high-level overview:
1. Set `PermitRootLogin` to `no`
2. Create a whitelist with `AllowUsers <user1> <user2>`
3. Create a blacklist with `DenyUsers <user1> <user2>`
4. Set `PermitEmptyPasswords` to `no`
5. Set `PasswordAuthentication` to `no`
	1. This requires using an ssh key, meaning devices must be pre-approved and password login is disabled
6. Set `AllowTcpForwarding` and `X11Forwarding` to `no`
7. Set `LogLevel` to `VERBOSE`
8. Display banners with `Banner /etc/issue`
9. Restart SSH: `systemctl restart ssh`

## Apache2
Apache configuration is located in `/etc/apache2/`

A hardening guide can be found [here](https://hostadvice.com/how-to/web-hosting/ubuntu/how-to-harden-your-apache-web-server-on-ubuntu-18-04/) but note that apache itself isn't usually vulnerable, it's whatever sites the apache service is hosting.

Ports can be set in `/etc/apache2/ports.conf`, and IPv6 can be disabled in this file by setting `Listen 80` and `Listen 443` to `Listen 0.0.0.0:80` and `Listen 0.0.0.0:443`

Installing `libapache2-mod-security2` and `libapache2-mod-evasive` and restarting apache2 can protect against DDOS and brute forcing.

Web servers are usually located in `/var/www/html/<website>` which can be accessed with `http://localhost:80/<website>`
## MySQL
Many websites use a MySQL database. Access the database with the `sudo mysql` command.

Make sure to change root password for the database:
```sql
UPDATE mysql.user SET Password=PASSWORD('<password>') WHERE User='root';
FLUSH PRIVILEGES;
```
If you get `ERROR 1054` use the following:
```sql
ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass';
FLUSH PRIVILEGES;
```

A guide to changing prestashop passwords can be found [here](https://www.inmotionhosting.com/support/edu/prestashop/prestashop-admin-password-database/). If we don't have cpanel or phpmyadmin access, just use SQL:
```sql
UPDATE `ps_employee` SET `passwd` = MD5('<_COOKIE_><password>') WHERE `ps_employee`.`id_employee` = 1;
```

Try to change passwords in the databases based on the services running. Figuring out how to do this will require research based on services.

**Note:** phpmyadmin is a great MySQL management software, it can be installed with the `phpmyadmin` package. A setup guide can be found [here](https://help.ubuntu.com/community/phpMyAdmin)
# IPTables/Firewall
IPtables is probably the best firewall we could use for linux, as it's the lowest level and supports logging, and is probably what red team will use.

IPtables logs can be read with the `dmesg` command

Note that iptables rules do not persist on reboot. Either install the `iptables-persistent` package or restore rules with `iptables-restore < /etc/iptables/rules`
## Base rules
Run these to remove all previous rules and to deny all packets except for established connections
```bash
# Set variables
IPTABLES=/sbin/iptables

# Remove previous firewall rules (run all commands in order)
$IPTABLES -F
$IPTABLES -F -t nat
$IPTABLES -X
$IPTABLES -P INPUT DROP
$IPTABLES -P OUTPUT DROP
$IPTABLES -P FORWARD DROP

# INPUT rules
# This sets logging for dropped packets and refines rules to exclusively allow an outgoing tcp session for web ports.
$IPTABLES -A INPUT -m state --state INVALID -j LOG --log-prefix "DROP INVALID " --log-ip-options --log-tcp-options
$IPTABLES -A INPUT -m state --state INVALID -j DROP
$IPTABLES -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
$IPTABLES -A INPUT ! -i lo -j LOG --log-prefix "DROP " --log-ip-options --log-tcp-options

# OUTPUT rules
$IPTABLES -A OUTPUT -m state --state INVALID -j LOG --log-prefix "DROP INVALID " --log-ip-options --log-tcp-options
$IPTABLES -A OUTPUT -m state --state INVALID -j DROP
$IPTABLES -A OUTPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
$IPTABLES -A OUTPUT ! -o lo -j LOG --log-prefix "DROP " --log-ip-options --log-tcp-options

# Save iptables rules
iptables-save > ~/rules
```
## Allowing incoming network connections for services
Note: Outgoing connections **are not required** for services to work using these rules, as an incoming packet will establish a connection that will be allowed.
```bash
# Set variables
IPTABLES=/sbin/iptables

# Allow pings
$IPTABLES -I INPUT 4 -p icmp --icmp-type echo-request -j ACCEPT
$IPTABLES -I OUTPUT 4 -p icmp --icmp-type echo-request -j ACCEPT

# Allow incoming TCP ports (use for services, e.g. port 22 for ssh, or 80 and 443 for web)
$IPTABLES -I INPUT 4 -p tcp --syn -m state --state NEW -j ACCEPT --dport <port>

# Allow incoming UDP ports
$IPTABLES -I INPUT 4 -p udp -m state --state NEW -j ACCEPT --dport <port>

# Save iptables rules
iptables-save > ~/rules
```
## Allow web traffic for package updates and installs
```bash
$IPTABLES -I OUTPUT 4 -p udp -m state --state NEW -j ACCEPT --dport 53
$IPTABLES -I OUTPUT 4 -p tcp --syn -m state -m multiport --state NEW -j ACCEPT --dports 80,443

# Save iptables
iptables-save > ~/rules
```
## Remove rules
```bash
# List all rules
iptables -L --line-numbers

# Delete rules
iptables -D <chain> <number>

# Example
iptables -D OUTPUT 4
```
## Command rule reference
For command references, we'll refer to a basic command and modify it to get whatever we want
### Base command
This appends a rule to the INPUT chain that accepts trafic to port 80:
```bash
$IPTABLES -A INPUT -j ACCEPT --dport 80
```
### Place rule in specific location
Use `-I <chain> <location>`
This places the base rule at the 4th spot of the chain, bumping the other rules down:
```bash
$IPTABLES -I INPUT 4 -j ACCEPT --dport 80
```
### Set specific protocol
Use `-p <protocol>`
This allows UDP packets only to port 80:
```bash
$IPTABLES -A INPUT -p tcp -j ACCEPT --dport 80
```
For TCP packets, you can also set a specific type of packet
This allows TCP SYN packets only to port 80:
```bash
$IPTABLES -A INPUT -p tcp --syn -j ACCEPT --dport 80
```
### Set state
Use `-m state --state <state>`
This allows only new connections (very useful for logging) to port 80:
```bash
$IPTABLES -A INPUT -j ACCEPT -m state --state NEW --dport 80
```
### Set action
Use `-j <action>`
This denies all packets to port 80:
```bash
$IPTABLES -A INPUT -j DROP --dport 80
```
### Set source
Use `-p <protocol> --sport <port>` for source ports, and `-s <ip or subnet>` for source IPs
This allows all TCP packets coming from port 80:
```bash
$IPTABLES -A INPUT -j ACCEPT -p tcp --sport 80
```
This allows all packets coming from 10.0.0.1:
```bash
$IPTABLES -A INPUT -j ACCEPT -s 10.0.0.1
```
### Set destination
Use `-m multiport -dports <port>` for dest ports
This allows all packets heading to port 443:
```bash
$IPTABLES -A INPUT -j ACCEPT --dport 443
```

# Fail2Ban IP Lockouts
- scans log files like `/var/log/auth.log` and bans IP addresses conducting too many failed login attempts

To install:

```
tar xvfj fail2ban-master.tar.bz2
cd fail2ban-master
sudo python setup.py install
```

Alternatively, you can clone the source from GitHub to a directory of Your choice, and do the install from there. Pick the correct branch, for example, master or 0.11

```
git clone https://github.com/fail2ban/fail2ban.git
cd fail2ban
sudo python setup.py install 
```

This will install Fail2Ban into the python library directory. The executable scripts are placed into `/usr/bin`, and configuration in `/etc/fail2ban`.

Fail2Ban should be correctly installed now. Just type:

```
fail2ban-client -h
```

Please note that the system init/service script is not automatically installed. To enable fail2ban as an automatic service, simply copy the script for your distro from the `files` directory to `/etc/init.d`. Example (on a Debian-based system):

```
cp files/debian-initd /etc/init.d/fail2ban
update-rc.d fail2ban defaults
service fail2ban start
```
# Backups
## Tar backups
[Reference](https://help.ubuntu.com/community/BackupYourSystem/TAR)
### Create backup
Note: Do NOT use a wildcard `*` when creating backups. This will write your backup with mostly empty data.
```bash
cd <directory> tar -cvpzf backup.tar.gz --exclude=/backup.tar.gz <folder>
```
### Restore backup
```bash
sudo tar -xvpzf backup.tar.gz -C /<directory_path> --numeric-owner
```
Backups can be sent over FTP(S), SFTP, or netcat

# LinPeas

- "**LinPEAS is a script that search for possible paths to escalate privileges on Linux/Unix*/MacOS hosts.**"

- Scans take about 5-10 minutes. 

## Installation
### One line execution
```bash
curl -L https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh | sh | less -R
```
### Executable download
```
python -c "import urllib.request; urllib.request.urlretrieve('https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh', 'linpeas.sh')"
```
**Make the file into an executable:**
```
chmod +x linpeas.sh
```
**Run linpeas.sh and output results to text file (Scan could take about 5-10 minutes):**
```
./linpeas.sh -a -r > /dev/shm/linpeas.txt
```
**Read the text file with color:**
```
less -R /dev/shm/linpeas.txt
```