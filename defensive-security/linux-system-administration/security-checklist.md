---
title: Security Checklist
description: 
published: true
date: 2024-02-23T18:42:39.830Z
tags: 
editor: markdown
dateCreated: 2024-02-23T04:36:27.056Z
---

# Service-focused
Note: see [OS info](os-info.md#bashrc) to get command history working on root, but please disable network connections and crontabs first.
## 1. Figure out/know ASAP what services are required
This should be in some sort of documentation, but if not, refer to the scoreboard and enumerate services with `ss -tulpn` and `ps flax`
## 2. Disable all network connections except required services
Note that iptables rules do not persist on reboot. Either install the `iptables-persistent` package or restore rules with `iptables-restore < /etc/iptables/rules`

Refer to the [IPtables guide](hardening.md#iptablesfirewall)
## 3. Remove crontabs
Refer to [Crontabs](os-info.md#crontab)
## 4. Check processes
Refer to [Processes](incident-response.md#processes)
## 5. Shutdown/disable all services
Refer to [Services](services.md#enabledisable-services)
## 6. Check shell files for any user you'll login as
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
## 7. Check users and groups
See the [OS info page](os-info.md#Passwords) for more information (please read the user and password sections before just determining if a user is 'good' or not)

Make sure to verify UIDs and ensure there aren't duplicates also.
```bash
# Show all duplicate UIDs
cut /etc/passwd -d ':' -f 3 | sort | uniq -d

# Easily see what user has the duplicate
grep ":<UID>:" /etc/passwd

# Print any user that has an empty password (add passwords to these users)
sudo cat /etc/shadow | awk -F: '($2==""){print $1}'
```
## 8. Check sudoers files
```bash
# Read file (you'll have to manually check for misconfigurations, use google or something unless I get a guide)
visudo

# Check sudoers directory (everything in here is executed as if it was in the /etc/sudoers file
ls -la /etc/sudoers.d/
```
## 9. Check ssh keys
```bash
# Check ssh directory for keys that can be used by redteam to sign in (we likely don't need ssh keys)
ls -la /root/.ssh
ls -la /home/<user>/.ssh
```
## 10. Set up auditd logging
Refer to [the logging guide](logging.md#set-up-auditd-logging)
## 11. Set up IDS/IPS or other useful tools/scripts
\[todo\]
## 12. Secure services
Refer to [Service Hardening](hardening.md#service-hardening)
## 13. Further Tasks (any order)
- Check pam files
- Set up [Fail2Ban](hardening.md#fail2ban-ip-lockouts)
- Verify [app logs](logging.md#application-logs) are working/enabled
- Create [backups](hardening.md#backups) of important files
- Check for [suspicious files](os-info.md#filename-whitespace-detector)
- Help with injects
- Secure [non-service-focused machines](security-checklist.md#not-service-focused-machines-that-dont-have-to-run-specific-services)
# Not service-focused (machines that don't have to run specific services)
Note: see [OS info](os-info.md#Misc) to get command history working on root, but please disable network connections and crontabs first.

Additionally, when hardening multiple machines, complete step one and two on each machine first before moving onto the next steps.
## 1. Disable all network connections
Refer to the [IPtables guide](hardening.md#iptablesfirewall) for more information
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

# Save iptables rules
iptables-save > ~/rules
```
## 2. Remove crontabs
Refer to [Crontabs](os-info.md#crontab)
## 3. Check processes
Refer to [Processes](incident-response.md#processes)
## 4. Shutdown/disable all services
Refer to [Services](services.md#enabledisable-services)
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
See the [OS info page](os-info.md#Passwords) for more information (please read the user and password sections before just determining if a user is 'good' or not)

Make sure to verify UIDs and ensure there aren't duplicates also.
```bash
# Show all duplicate UIDs
cut /etc/passwd -d ':' -f 3 | sort | uniq -d

# Easily see what user has the duplicate
grep ":<UID>:" /etc/passwd

# Print any user that has an empty password (add passwords to these users)
sudo cat /etc/shadow | awk -F: '($2==""){print $1}'
```
## 7. Check sudoers files
```bash
# Read file (you'll have to manually check for misconfigurations, use google or something unless I get a guide)
visudo

# Check sudoers directory (everything in here is executed as if it was in the /etc/sudoers file
ls -la /etc/sudoers.d/
```
## 8. Check ssh keys
```bash
# Check ssh directory for keys that can be used by redteam to sign in (we likely don't need ssh keys)
ls -la /root/.ssh
ls -la /home/<user>/.ssh
```
## 9. Allow web traffic (deny pings still)
Refer to the [IPtables guide](hardening.md#iptablesfirewall)
## 10. Patch system
Refer to [updates and installs](os-info.md#updates-and-installs)
## 11. Set up auditd logging
Refer to [the logging guide](logging.md#set-up-auditd-logging)
## 12. Set up IDS/IPS or other useful tools/scripts
\[todo\]
## 13. Further tasks (any order)
- Check pam files
- Set up [Fail2Ban](hardening.md#fail2ban-ip-lockouts)
- Verify [app logs](logging.md#application-logs) are working/enabled
- Create [backups](hardening.md#backups) of important files
- Check for [suspicious files](os-info.md#filename-whitespace-detector)
- Help with injects