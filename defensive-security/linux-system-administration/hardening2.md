---
title: hardening2
description: 
published: true
date: 2024-02-22T16:22:41.902Z
tags: 
editor: markdown
dateCreated: 2024-02-22T06:13:31.452Z
---

# Hardening

## Fail2Ban 

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

# Info Gathering
## OS
```bash
uname -a
cat /etc/issue
cat /etc/*-release*
```
## Installed software
```bash
dpkg -l # Debian/Ubuntu
rpm -qa # RedHat/CentOS
```
## Running Services
```bash
ps flax
netstat -tupan
```
- `-t`: Show TCP ports.
- `-u`: Show UDP ports.
- `-l`: Show only listening ports.
- `-p`: Show the PID and name of the process to which each socket belongs.
- `-a`: Show both listening and non-listening (for TCP this means established) sockets.
- `-n`: Show numerical addresses instead of resolving hosts.
- `-o`: Show the owning process ID associated with each connection.
## Find listening PIDs
```bash
ss -nlp | grep -E 'LISTEN.*([0-9]+\.){3}[0-9]+:[0-9]+' | grep -e 'pid=[0-9]*'
```
## Scheduled Tasks
```bash
crontab -l
ls -alh /etc/cron*
```
- crontab -l   (it's an L, not an i.) shows the contents of a crontab file


## Users and Groups
```bash
cat /etc/passwd
cat /etc/group
id
```
## Networking and Firewalls
```bash
netstat -ano

#Reject TCP for ports on Linux 
# IPtables is volatile, so save before 
iptables -I INPUT -p tcp --dport <port> -j REJECT --reject-with tcp-reset

```
## Environment Variables
```bash
env
```
# File and Directory Perms
## World Writable Files
```bash
find / -type f -perm -2 ! -path "/proc/*" -exec ls -l {} 2</dev/null \;
```
## SUID/SGID Files
```bash
find / -type f -perm -4000 ! -path "/proc/*" -exec ls {} 2</dev/null \; # SUID
find / -type f -perm -2000 ! -path "/proc/*" -exec ls {} 2</dev/null \; # SGID
```
## Sudo Permissions
```bash
sudo -l
```
 - -l (L, not i. -l creates a list. )
## Configuration Files
```bash
cat /etc/sudoers
ls -alh /var/www/
ls -alh /var/mail/
ls -alh /var/spool/
```
## Password Files
```bash
cat /etc/shadow
cat /etc/master.passwd
```
# Kernel Exploits
```bash
searchsploit [kernel version]
```
# Custom Applications
```bash
file [binary name]
strings [binary name]
ldd [binary name]
```
# Database Access
```bash
cat /var/www/config.php
```
# Scanning
```bash
#Find open ports
sudo nmap 10.10.11.x -g 53 -T5 -Pn --disable-arp-ping -vv -p-

# Enumerate those ports
sudo nmap 10.10.11.x -g 53 -T5 -Pn --disable-arp-ping -vv -A -p <ports> 

#Scan only the top (insert number) most common ports
sudo nmap 10.10.11.x  -g 53 -T5 -Pn --top-ports <number> -vv -A -p-



```

## SCANNING INFO
**IF THE NETWORK IS TOO SLOW AND NMAP IS FAILING TO PROPERLY SCAN: **
- Decrease from -T5 (fastest scans), to -T4 or lower. 
- Scan only the top (insert number) most common ports"

**IMPORTANT INFO**
- https://nmap.org/book/man-briefoptions.html Source on important Nmap Switches
- Make sure to run scans using sudo, otherwise, Nmap will default to regular TCP scans. 
## Firewall Evasion Methods when Scanning

 The ```-Pn```  switch tells Nmap to not bother pinging the host before scanning it.
- So Nmap will treat the host as always being alive, bypassing the ICMP block.

- **Cons:**
* It takes a long time to scan, because Nmap will double check each port


	**-f Switch / --mtu**
- `-f` Fragments the packets, which makes detection less likely
- An alternative is ```--mtu <number>```, which provides more control over the size of the packets.
- ```--scan-delay <time>ms``` creates delays in between packets. Good for unstable networks, but also for avoiding time-based firewall/IDS triggers
		



