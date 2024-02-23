---
title: Security Checklist
description: 
published: true
date: 2024-02-23T04:36:27.056Z
tags: 
editor: markdown
dateCreated: 2024-02-23T04:36:27.056Z
---

# Service-focused


# Not service-focused (machines that don't have to run specific services)
Note: see [OS info](os-info#Misc) to get command history working on root, but please disable network connections and crontabs first.

Additionally, when hardening multiple machines, complete step one and two on each machine first before moving onto the next steps.
## 1. Disable any network connections
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
```
## 2. Remove crontabs
```bash
# List then remove all user crontabs
for user in $(cut -f1 -d: /etc/passwd); do echo $user; crontab -u $user -l; crontab -u $user -r; done

# System crontabs are also stored in /etc/cron*
# See those with the following:
ls /etc/cron*
```
## 3. Check processes
```bash
# List processes and command line used, check startup times too and make sure they check out
# Note that red team can change startup times with clever tricks but this can be logged
ps faxo user,uid,pid,ppid,tt,start,exe,command

# Kill processes
kill -9 <pid>
```
## 4. Shutdown/disable all services
```bash
# List running services
systemctl | grep running | grep service

# Disable and stop services (note they can still be run)
systemctl disable --now <service>

# Permanently disable service
systemctl stop <service>; systemctl mask <service>
```
## 5. Check shell files for any user you'll login as
```bash
# Note that executable code can be inserted into any files that will run when you `cat` the file.
# To counteract this, read files using an editor like vi, vim, nano, etc

# Bash (most common)
vi /root/.bashrc
vi /home/<user>/.bashrc

# Sh
vi /root/.shrc
vi /home/<user>/.shrc

# Zsh
vi /root/.zshrc
vi /home/<user>/.zshrc
```
## 6. Check users and groups
See the [OS info page](os-info#Passwords) for more information (please read the user and password sections before just determining if a user is 'good' or not)

Make sure to verify UIDs and ensure there aren't duplicates:
```bash
# Show all duplicate UIDs
cut /etc/passwd -d ':' -f 3 | sort | uniq -d

# Easily see what user has the duplicate
grep ":<UID>:" /etc/passwd
```
## 7. Check sudoers files
```bash

```
## 8. Check ssh keys
```bash

```
## 9. Allow web traffic 
```bash

```

## 10. Patch system
```bash

```