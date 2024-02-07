# Hardening

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
ps aux
netstat -tupan
```
- `-t`: Show TCP ports.
- `-u`: Show UDP ports.
- `-l`: Show only listening ports.
- `-p`: Show the PID and name of the process to which each socket belongs.
- `-a`: Show both listening and non-listening (for TCP this means established) sockets.
- `-n`: Show numerical addresses instead of resolving hosts.
- `-o`: Show the owning process ID associated with each connection.
## Scheduled Tasks
```bash
crontab -l
ls -alh /etc/cron*
```
## Users and Groups
```bash
cat /etc/passwd
cat /etc/group
id
```
## Networking and Firewalls
```bash
netstat -ano
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