---
title: Hardening 2 Electric Boogaloo
description: 
published: true
date: 2024-02-23T14:40:56.865Z
tags: 
editor: markdown
dateCreated: 2024-02-22T06:13:31.452Z
---

# Fail2Ban 

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