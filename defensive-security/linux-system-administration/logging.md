---
title: Logging
description: 
published: true
date: 2024-11-09T06:20:04.153Z
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

# If auditd won't restart, open /usr/lib/systemd/system/auditd.service and set RefuseManualStop=no
# Then run systemctl daemon-reload
# Restart server, then set RefuseManualStop back to yes and run systemctl daemon-reload
```
### One line configuration (install it first):
```bash
sudo rm /etc/audit/audit.rules && sudo sh -c 'curl https://raw.githubusercontent.com/Neo23x0/auditd/master/audit.rules > /etc/audit/audit.rules' && sudo sh -c 'echo "-a always,exit -F arch=b64 -S execve -k command" >> /etc/audit/audit.rules' && sudo chattr +i /etc/audit/audit.rules && sudo systemctl enable --now auditd && sudo systemctl restart auditd
```
Note that this will *almost always* 

## Extract auditd command execution events to ~/command_executions
```bash
grep -E 'EXECVE' -B 1 -A 3 /var/log/audit/audit.log > ~/command_executions; sed -i -e 's/^type=SYSCALL/\nSyscall/' -e 's/^type=EXECVE/Command/' -e 's/^type=CWD/Current_directory/' -e 's/^type=PATH/Path/' ~/command_executions; awk '{match($0, /msg=audit\(([0-9]+)\.[0-9]+:[0-9]+\)/, a); $0=gensub(/msg=audit\([0-9]+\.[0-9]+:[0-9]+\)/, strftime("%H:%M:%S", a[1]), "g"); print}' ~/command_executions > ~/command_executions2; mv ~/command_executions2 ~/command_executions; sed -i -e 's/arch=.* success/success/' -e 's/exit=.* ppid/ppid/' -e 's/ comm=/ command=/' -e 's/ cwd=//' -e 's/inode.*ouid/ouid/' -e 's/ rdev=.*//' -e 's/argc=/arguments=/' ~/command_executions
```
# Ausearch
`ausearch` is used to search through the auditd log output

To search through auditd logs, simply run `ausearch -i` and use one of the search flags

Example:
```bash
ausearch -i -k susp_activity
```
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

# Splunk

Splunk is basically a search engine for log files, allowing you to search logs and other data across systems.

## Searches
### Command executions
This is my magnum opus for logging:
```spl
index=* sourcetype="auditd" type="EXECVE" a0!="ps" a0!="sh" a0!="/bin/bash" a0!="/bin/sh" a0!="pgrep" a0!="grep" a0!="cut" a0!="ss"
| eval foo=""
| eval a2 = coalesce(a2,foo)
| eval a4 = coalesce(a4,foo)
| eval a2 = urldecode(replace(a2,"([0-9A-F]{2})","%\1"))
| fields - argc
| search a2!="ps*" a2!="pgrep*" a2!="grep*" a2!="cut*" a4!="/run/containerd/io.containerd.runtime*"
| foreach a*
    [| eval cmdline=if(isnull(cmdline),'<<FIELD>>',if(isnull(<<FIELD>>),cmdline,cmdline + " " + '<<FIELD>>')) ]
| search cmdline!="*command_execution*" cmdline!="sed -e s/ \\ \\ /\\ /g " cmdline!="cat /etc/group  " cmdline!="cat /etc/passwd  " cmdline!="/opt/gitlab/embedded/bin/curl --fail --max-time 10 --insecure https://localhost:443/help" cmdline!="runc init  " cmdline!="/lib/systemd/systemd-user-runtime-dir stop  " cmdline!="sleep 600  " cmdline!="/usr/bin/env dir=/var/opt/gitlab/logrotate pre_sleep=`0 post_sleep=3000 /opt/gitlab/embedded/bin/gitlab-logrotate-wrapper" cmdline!="/opt/gitlab/embedded/bin/chpst -P /usr/bin/env dir=/var/opt/gitlab/logrotate pre_sleep=600 post_sleep=3000 /opt/gitlab/embedded/bin/gitlab-logrotate-wrapper"
| table host,_time,cmdline
```
### Manual logs (from crontab)
```spl
index=* sourcetype=manual
```
### Login sessions (/var/log/secure)
```spl
index=* sourcetype=linux_secure
```
### Cron execution logs
```spl
index=* sourcetype=cron
```


## Ubuntu installation

Run the following oneliners to install Splunk on a fresh Ubuntu server:

### Enterprise Server
```bash
sudo apt-get update && sudo apt-get install ca-certificates curl && sudo install -m 0755 -d /etc/apt/keyrings && sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc && sudo chmod a+r /etc/apt/keyrings/docker.asc && echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null && sudo apt-get update && sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin && sudo docker run -d -p 8000:8000 -p 9997:9997 -e "SPLUNK_PASSWORD=password" -e "SPLUNK_START_ARGS=--accept-license" -it --name so1 splunk/splunk:latest
```
### Universal Forwarder
```bash
sudo apt-get update && sudo apt-get install ca-certificates curl && sudo install -m 0755 -d /etc/apt/keyrings && sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc && sudo chmod a+r /etc/apt/keyrings/docker.asc && echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null && sudo apt-get update && sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin && sudo docker run -d -v /:/ufhost/ --name uf1 --hostname uf1 -e "SPLUNK_PASSWORD=password" -e "SPLUNK_START_ARGS=--accept-license" -e "SPLUNK_STANDALONE_URL=192.168.69.137" -it splunk/universalforwarder:latest
```

## Linux inputs.conf
Here's a config file that should work on the linux universal forwarders for simple data ingest
```ini
[monitor:///var/log]

[monitor:///var/log/audit]
sourcetype = auditd

[monitor:///var/log/httpd]
sourcetype = httpd

[monitor:///var/log/manual]
sourcetype = manual

[fschange:///etc/]
fullEvent=true
pollPeriod=60
recurse=true
sendEventMaxSize=10000
index=filechange
sourcetype = etc_change

[fschange:///var/www]
fullEvent=true
pollPeriod=60
recurse=true
sendEventMaxSize=10000
index=filechange
sourcetype = www_change

[blacklist:$SPLUNK_HOME/etc/auth]

[blacklist:$SPLUNK_HOME/etc/passwd]

[monitor://$SPLUNK_HOME/var/log/splunk]
index = _internal

[monitor://$SPLUNK_HOME/var/log/watchdog/watchdog.log*]
index = _internal

[monitor://$SPLUNK_HOME/var/log/splunk/license_usage_summary.log]
index = _telemetry

[monitor://$SPLUNK_HOME/var/log/splunk/splunk_instrumentation_cloud.log*]
index = _telemetry
sourcetype = splunk_cloud_telemetry

[monitor://$SPLUNK_HOME/etc/splunk.version]
_TCP_ROUTING = *
index = _internal
sourcetype=splunk_version

[batch://$SPLUNK_HOME/var/run/splunk/search_telemetry/*search_telemetry.json]
move_policy = sinkhole
index = _introspection
sourcetype = search_telemetry
crcSalt = <SOURCE>
log_on_completion = 0

[batch://$SPLUNK_HOME/var/spool/splunk]
move_policy = sinkhole
crcSalt = <SOURCE>

[batch://$SPLUNK_HOME/var/spool/splunk/tracker.log*]
index = _internal
sourcetype = splunkd_latency_tracker
move_policy = sinkhole

[batch://$SPLUNK_HOME/var/spool/splunk/...stash_new]
queue = stashparsing
sourcetype = stash_new
move_policy = sinkhole
crcSalt = <SOURCE>
time_before_close = 0

[batch://$SPLUNK_HOME/var/spool/splunk/...stash_hec]
sourcetype = stash_hec
move_policy = sinkhole
crcSalt = <SOURCE>

[fschange:$SPLUNK_HOME/etc]
disabled = false
#poll every 10 minutes
pollPeriod = 600
#generate audit events into the audit index, instead of fschange events
signedaudit=true
recurse=true
followLinks=false
hashMaxSize=-1
fullEvent=false
sendEventMaxSize=-1
filesPerDelay = 10
delayInMills = 100
```

## Windows inputs.conf
```
# Use file system change monitor:

[fschange:/etc/]
fullEvent=true
pollPeriod=60
recurse=true
sendEventMaxSize=100000
index=main

# Monitor the Security Windows Event Log channel, getting the most recent
# events first, then older, and finally continuing to gather newly arriving events

[WinEventLog://Security]
    renderXml = true
    disabled = false
    evt_resolve_ad_obj = true
    blacklist1 = EventCode="4662" Message="Object Type:\s+(?!groupPolicyContainer)"
    blacklist2 = EventCode="566" Message="Object Type:\s+(?!groupPolicyContainer)"
    blacklist3 = EventCode="4688" Message="New Process Name: (?i)(?:[C-F]:\Program Files\Splunk(?:UniversalForwarder)?\bin\(?:btool|splunkd|splunk|splunk-(?:MonitorNoHandle|admon|netmon|perfmon|powershell|regmon|winevtlog|winhostinfo|winprintmon|wmi)).exe)"
    #index = 

[WinEventLog://Application]
    renderXml = true
    disabled = false
    #index = 

[WinEventLog://System]
    renderXml = true
    disabled = false
    #index = 

[WinEventLog://Microsoft-Windows-Sysmon/Operational]
disabled = false
renderXml = true
source = XmlWinEventLog:Microsoft-Windows-Sysmon/Operational

[blacklist:$SPLUNK_HOME/etc/auth]

[blacklist:$SPLUNK_HOME/etc/passwd]

[monitor://$SPLUNK_HOME/var/log/splunk]
index = _internal

[monitor://$SPLUNK_HOME/var/log/watchdog/watchdog.log*]
index = _internal

[monitor://$SPLUNK_HOME/var/log/splunk/license_usage_summary.log]
index = _telemetry

[monitor://$SPLUNK_HOME/var/log/splunk/splunk_instrumentation_cloud.log*]
index = _telemetry
sourcetype = splunk_cloud_telemetry

[monitor://$SPLUNK_HOME/etc/splunk.version]
_TCP_ROUTING = *
index = _internal
sourcetype=splunk_version

[batch://$SPLUNK_HOME/var/run/splunk/search_telemetry/*search_telemetry.json]
move_policy = sinkhole
index = _introspection
sourcetype = search_telemetry
crcSalt = <SOURCE>
log_on_completion = 0

[batch://$SPLUNK_HOME/var/spool/splunk]
move_policy = sinkhole
crcSalt = <SOURCE>

[batch://$SPLUNK_HOME/var/spool/splunk/tracker.log*]
index = _internal
sourcetype = splunkd_latency_tracker
move_policy = sinkhole

[batch://$SPLUNK_HOME/var/spool/splunk/...stash_new]
queue = stashparsing
sourcetype = stash_new
move_policy = sinkhole
crcSalt = <SOURCE>
time_before_close = 0

[batch://$SPLUNK_HOME/var/spool/splunk/...stash_hec]
sourcetype = stash_hec
move_policy = sinkhole
crcSalt = <SOURCE>

[fschange:$SPLUNK_HOME/etc]
disabled = false
#poll every 10 minutes
pollPeriod = 600
#generate audit events into the audit index, instead of fschange events
signedaudit=true
recurse=true
followLinks=false
hashMaxSize=-1
fullEvent=false
sendEventMaxSize=-1
filesPerDelay = 10
delayInMills = 100
```

## outputs.conf
Here's a config file that should work for log forwarding (make sure to change the server ip)
```ini
[indexAndForward]
index = false

[tcpout]
defaultGroup = default-autolb-group

[tcpout:default-autolb-group]
server = 10.0.23.143:9997

[tcpout-server://10.0.23.143:9997]
```

## /etc/crontab
Here's a crontab to get more logs
```
SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root

# For details see man 4 crontabs

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name  command to be executed

*/1 * * * *     root    /root/commands.sh
*/1 * * * *     root    cat /etc/passwd | cut -d ':' -f 1 > /var/log/manual/usernames.log
*/1 * * * *     root    cat /etc/group > /var/log/manual/groups.log
*/1 * * * *     root    ss -nlp | grep -E 'LISTEN.*(([0-9]+\.){3}[0-9]+|\*|):[0-9]+' | sed -e 's/ \ \ /\ /g' | grep -e 'pid=[0-9]*' > /var/log/manual/listening_processes.log
*/1 * * * *     root    ps aux > /var/log/manual/processes.log
*/1 * * * *     root    ss -tupan > /var/log/manual/network_connections.log
```
### /root/commands.sh
This gets all command executions from auditd and puts it into a log file
```bash
#!/bin/bash
grep -E 'EXECVE' -B 1 -A 3 /var/log/audit/audit.log > /tmp/command_executions; sed -i -e 's/^type=SYSCALL/\nSyscall/' -e 's/^type=EXECVE/Command/' -e 's/^type=CWD/Current_directory/' -e 's/^type=PATH/Path/' /tmp/command_executions; awk '{match($0, /msg=audit\(([0-9]+)\.[0-9]+:[0-9]+\)/, a); $0=gensub(/msg=audit\([0-9]+\.[0-9]+:[0-9]+\)/, strftime("%H:%M:%S", a[1]), "g"); print}' /tmp/command_executions > /tmp/command_executions2; mv -f /tmp/command_executions2 /tmp/command_executions; sed -i -e 's/arch=.* success/success/' -e 's/exit=.* ppid/ppid/' -e 's/ comm=/ command=/' -e 's/ cwd=//' -e 's/inode.*ouid/ouid/' -e 's/ rdev=.*//' -e 's/argc=/arguments=/' /tmp/command_executions; mv -f /tmp/command_executions /var/log/audit/command_executions.log
```