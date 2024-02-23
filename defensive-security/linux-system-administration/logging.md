---
title: Logging
description: 
published: true
date: 2024-02-23T07:34:12.604Z
tags: 
editor: markdown
dateCreated: 2024-02-22T06:13:40.961Z
---

# Application logs
Application logs are stored in /var/log/\<application\>
# Set up auditd logging
## Install
### Ubuntu:
```bash
sudo apt-get install -y auditd audispd-plugins
```
### Centos/Fedora:
```bash
sudo yum install -y auditd audispd-plugins
```
## Configure log files
```bash
# Download config
rm /etc/audit/audit.rules
curl https://raw.githubusercontent.com/Neo23x0/auditd/master/audit.rules > /etc/audit/audit.rules

# Open config in any editor
vi /etc/audit/audit.rules
# Add the following lines to the end of the file
-a always,exit -F arch=b64 -S execve -k command


# Make config immutable
sudo chattr +i /etc/audit/audit.rules

# Enable/restart auditd
sudo systemctl enable --now auditd
sudo systemctl restart auditd

# If auditd won't restart, open /usr/lib/systemd/system/auditd.service and set RefuseManualStop=yes
# Then run systemctl daemon-reload
```
# Extract auditd command execution events to ~/command_executions
```bash
grep -E 'EXECVE' -B 1 -A 3 /var/log/audit/audit.log > ~/command_executions; sed -i -e 's/^type=SYSCALL/\nSyscall/' -e 's/^type=EXECVE/Command/' -e 's/^type=CWD/Current_directory/' -e 's/^type=PATH/Path/' ~/command_executions; awk '{match($0, /msg=audit\(([0-9]+)\.[0-9]+:[0-9]+\)/, a); $0=gensub(/msg=audit\([0-9]+\.[0-9]+:[0-9]+\)/, strftime("%H:%M:%S", a[1]), "g"); print}' ~/command_executions > ~/command_executions2; mv ~/command_executions2 ~/command_executions; sed -i -e 's/arch=.* success/success/' -e 's/exit=.* ppid/ppid/' -e 's/ comm=/ command=/' -e 's/ cwd=//' -e 's/inode.*ouid/ouid/' -e 's/ rdev=.*//' -e 's/argc=/arguments=/' ~/command_executions
```
# Ausearch
`ausearch` is used to search through the auditd log output
## Ausearch flags
```bash
-i	interpret (Hex->Text)
-k	Search based on key
-p	Search given process ID
-pp	Search given parent process ID
-m	Search given message type (use all for all messages)
-sv	Search given success value (yes or no)
-ua	Search using user ID, euid, login id, or auid
-ts	Search for events w/ time stamps =/+ given end time
```
## Most important ausearch keys
```
cron: Cron configuration & scheduled jobs

etcgroup: /etc/group and /etc/gshadow changes
etcpasswd: /etc/passwd and /etc/shadow changes
opasswd: /etc/security/opasswd changes

actions: Sudoers file changes

passwd_modification: Password changes

group_modification: User/group change/creation

login: Login configuration and information

network_modifications: Network config changes

remote_shell: Detect Remote Shell Use

network_connect_4 or network_connect_6: Successful IPv4/6 Connections

etcissue: Changes to issue

init: System startup scripts

pam: Pam configuration

systemd: Systemd/service usage

priv_esc: Process ID change

recon: Reconnaissance

susp_activity: Suspicious activity (curl, base64, nc and more)

susp_shell: Suspicious shell

detect_execve_www: Web server activity

rootcmd: Root command executions

command: Non-root command executions
```
## All ausearch keys
These are placed in the order of the rules, as the first hit will take priority
### Main rules
```
auditlog: Attempts to read audit records

auditconfig: Modifications to audit configuration

audittools: Monitor audit management tools

sysctl: Kernel parameters

modules: Kernel module loading and unloading

modprobe: Modprobe configuration

specialfiles: Special files

T1078_Valid_Accounts: NFS Mount, sssd, vte, dbus, setfiles

swap: Change Swap

time: Time

stunnel: Stunnel

cron: Cron configuration & scheduled jobs

etcgroup: /etc/group and /etc/gshadow changes
etcpasswd: /etc/passwd and /etc/shadow changes
opasswd: /etc/security/opasswd changes

actions: Sudoers file changes

passwd_modification: Password changes

group_modification: User/group change/creation

login: Login configuration and information

network_modifications: Network config changes

remote_shell: Detect Remote Shell Use

network_connect_4 or network_connect_6: Successful IPv4/6 Connections

etcissue: Changes to issue

init: System startup scripts

libpath: Library search paths

systemwide_preloads: Systemwide library preloads (LD_PRELOAD)

pam: Pam configuration

mail: Mail configuration

sshd: ssh config

rootkey: root ssh key tampering

systemd: Systemd/service usage

systemd_generator: https://systemd.network/systemd.generator.html

mac_policy: SELinux events that modify the system's Mandatory Access Controls (MAC)

unauthedfileaccess: Access failures

priv_esc: Process ID change

power: Power state

session: Session initiation information

perm_mod: Discretionary Access Control (DAC) modifications
```
### Special rules
```
recon: Reconnaissance

susp_activity: Suspicious activity (curl, base64, nc and more)

Data_Compressed: Data compression

dbus_send and gdubs_call: May indicate priv esc

pkexec: May indicate priv esc

susp_shell: Suspicious shell

detect_execve_www: Web server activity

shell_profiles: Shell configuration

code_injection and data_injection and register_injection and tracing: Injection

anon_file_create: Anonymous file creation

power_abuse: (not useful for competitions) Detects if an admin is looking in a user's home dir

network_socket_created: Sockets

software_mgmt and third_party_software_mgmt: Software

string_search: Credentials in files

docker: docker

Inter-Process_Communication: Process communication

rootcmd: Root command executions

delete: File deletion events

file_access: Unsuccessful file access events

file_creation: Unsuccessful file creation events

file_modification: Unsuccessful file modification

32bit_api: Detects exploitation of 32 bit api

command: Non-root command execution
```