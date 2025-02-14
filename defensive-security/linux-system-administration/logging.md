---
title: Logging
description: 
published: true
date: 2025-02-14T01:25:17.804Z
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
grep -E 'EXECVE' -B 1 -A 3 /var/log/audit/audit.log* > ~/command_executions; sed -i -e 's/^type=SYSCALL/\nSyscall/' -e 's/^type=EXECVE/Command/' -e 's/^type=CWD/Current_directory/' -e 's/^type=PATH/Path/' ~/command_executions; awk '{match($0, /msg=audit\(([0-9]+)\.[0-9]+:[0-9]+\)/, a); $0=gensub(/msg=audit\([0-9]+\.[0-9]+:[0-9]+\)/, strftime("%H:%M:%S", a[1]), "g"); print}' ~/command_executions > ~/command_executions2; mv ~/command_executions2 ~/command_executions; sed -i -e 's/arch=.* success/success/' -e 's/exit=.* ppid/ppid/' -e 's/ comm=/ command=/' -e 's/ cwd=//' -e 's/inode.*ouid/ouid/' -e 's/ rdev=.*//' -e 's/argc=/arguments=/' ~/command_executions
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
## Verbose Splunk Installation for Linux Systems
Installing universal forwarder to send logs to Splunk server
helpful youtube video:
[https://www.youtube.com/watch?v=yl3gY3JEtmU](https://www.youtube.com/watch?v=yl3gY3JEtmU)

### Add splunk user and group

```bash
useradd -m splunkfwd
groupadd splunkfwd
```

### Install 
```bash
sudo su
export SPLUNK_HOME="/opt/splunkforwarder"
mkdir $SPLUNK_HOME./spl


wget -O splunkforwarder-9.3.1-0b8d769cb912-Linux-x86_64.tgz "https://download.splunk.com/products/universalforwarder/releases/9.3.1/linux/splunkforwarder-9.3.1-0b8d769cb912-Linux-x86_64.tgz"

tar xvzf splunkforwarder-9.3.1-0b8d769cb912-Linux-x86_64.tgz
```

copy contents of /splunkforwarder to /$SPLUNK_HOME once unzipped using tar



### Change owner to the splunk user

``` bash
chown -R splunkfwd:splunkfwd $SPLUNK_HOME
```

### Start 
```bash
sudo $SPLUNK_HOME/bin/splunk start --accept-license
```

### edit or create inputs config file 
location of inputs (and outputs) file: 
**$SPLUNK_HOME/etc/system/local/inputs.conf)**

(input file example further below)



### Connect to Receiving Indexer 
### Examples:
SPLUNK_HOME/bin/splunk add forward-server idx1.mycompany.com:9997
SPLUNK_HOME/bin/splunk add forward-server [HOSTNAME OR IP ADDRESS]:[LISTENING PORT]
  
## Linux inputs.conf
Here's a config file that should work on the linux universal forwarders for simple data ingest
```ini
[default]
host = XXX_XXXX_10.0.xxx.xxx

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
[default]
host = XXX_XXXX_10.0.xxx.xxx

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

[WinRegMon]
disabled = 0
hive=NULL
proc = .*
type = rename|set|delete|create
baseline = 1
baseline_interval = 3000

[perfmon://LocalPhysicalDisk]
interval = 10
object = PhysicalDisk
counters = Disk Bytes/sec; % Disk Read Time; % Disk Write Time; % Disk Time
instances = *
disabled = 0
index = PerfMon

# Gather common memory statistics using the Memory performance object, every
# 5 seconds. Store the data in the "main" index. Since none of the counters
# specified have applicable instances, the instances attribute is not required.

[perfmon://LocalMainMemory]
interval = 5
object = Memory
counters = Committed Bytes; Available Bytes; % Committed Bytes In Use
disabled = 0
index = main

# Gather data on USB activity levels every 10 seconds. Store this data in the
# default index.

[perfmon://USBChanges]
interval = 10
object = USB
counters = Usb Control Data Bytes/Sec
instances = *
disabled = 0

[WinRegMon://reg_1]
hive=\\REGISTRY\\MACHINE\\CurrentControlSet\\Services\\UsbStor\\?.*
[WinRegMon://reg_2]
hive=\\REGISTRY\\MACHINE\\Software\\Classes\\AllFileSystemObjects\\ShellEx\\?.*
[WinRegMon://reg_3]
hive=\\REGISTRY\\MACHINE\\Software\\Classes\\CLSID\\?.*
type = create|set
[WinRegMon://reg_4]
hive=\\REGISTRY\\MACHINE\\Software\\Classes\\CLSID\\\{083863F1-70DE-11d0-BD40-00A0C911CE86\}\\Instance\\?.*
[WinRegMon://reg_5]
hive=\\REGISTRY\\MACHINE\\Software\\Classes\\Directory\\ShellEx\\?.*
[WinRegMon://reg_6]
hive=\\REGISTRY\\MACHINE\\Software\\Classes\\Folder\\ShellEx\\?.*
[WinRegMon://reg_7]
hive=\\REGISTRY\\MACHINE\\Software\\Classes\\Htmlfile\\Shell\\Open\\Command\\?.*
[WinRegMon://reg_8]
hive=\\REGISTRY\\MACHINE\\Software\\Classes\\Protocols\\Filter\\?.*
[WinRegMon://reg_9]
hive=\\REGISTRY\\MACHINE\\Software\\Classes\\Protocols\\Handler\\?.*
[WinRegMon://reg_10]
hive=\\REGISTRY\\MACHINE\\Software\\Classes\\.*\\ShellEx\\?.*
[WinRegMon://reg_11]
hive=\\REGISTRY\\MACHINE\\Software\\Clients\\Mail\\?.*
[WinRegMon://reg_12]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\Active Setup\\Installed Components\\?.*
[WinRegMon://reg_13]
hive=\\REGISTRY\\MACHINE\\SOFTWARE\\Microsoft\\Cryptography\\OID\\?.*
[WinRegMon://reg_14]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\Cryptography\\OID\\EncodingType 0\\CryptSIPDllGetSignedDataMsg\\\{SIP Guid\}\\?.*
[WinRegMon://reg_15]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\Cryptography\\OID\\EncodingType 0\\CryptSIPDllVerifyIndirectData\\\{SIP Guid\}\\?.*
[WinRegMon://reg_16]
hive=\\REGISTRY\\MACHINE\\SOFTWARE\\Microsoft\\Cryptography\\Providers\\Trust\\?.*
[WinRegMon://reg_17]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\Cryptography\\Providers\\Trust\\FinalPolicy\\\{SIP Guid\}\\?.*
[WinRegMon://reg_18]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\EnterpriseCertificates\\AuthRoot\\Certificates\\?.*
[WinRegMon://reg_19]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\EnterpriseCertificates\\CA\\Certificates\\?.*
[WinRegMon://reg_20]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\EnterpriseCertificates\\Root\\Certificates\\?.*
[WinRegMon://reg_21]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\Internet Explorer\\Toolbar\\?.*
[WinRegMon://reg_22]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\.NETFramework\\?.*
[WinRegMon://reg_23]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\Office\\Excel\\Addins\\?.*
type = create|set
[WinRegMon://reg_24]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\Office\\Outlook\\Addins\\?.*
type = create|set
[WinRegMon://reg_25]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\Office\\PowerPoint\\Addins\\?.*
type = create|set
[WinRegMon://reg_26]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\Office\\Word\\Addins\\?.*
type = create|set
[WinRegMon://reg_27]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\SystemCertificates\\AuthRoot\\Certificates\\?.*
[WinRegMon://reg_28]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\SystemCertificates\\CA\\Certificates\\?.*
[WinRegMon://reg_29]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\SystemCertificates\\Root\\Certificates\\?.*
[WinRegMon://reg_30]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\Terminal Server Client\\?.*
[WinRegMon://reg_31]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\VBA\\Monitors\\?.*
[WinRegMon://reg_32]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\WBEM\\CIMOM\\[^\\]*
[WinRegMon://reg_33]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\WBEM\\ESS\\?.*
type = create|set
[WinRegMon://reg_34]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\Windows\\CurrentVersion\\App Paths\\?.*
[WinRegMon://reg_35]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\Windows\\CurrentVersion\\Authentication\\Credential Provider Filters\\?.*
[WinRegMon://reg_36]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\Windows\\CurrentVersion\\Authentication\\Credential Providers\\?.*
[WinRegMon://reg_37]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\Windows\\CurrentVersion\\Authentication\\PLAP Providers\\?.*
[WinRegMon://reg_38]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\Windows\\CurrentVersion\\Explorer\\Browser Helper Objects\\?.*
[WinRegMon://reg_39]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\Windows\\CurrentVersion\\Explorer\\ShellExecuteHooks\\?.*
[WinRegMon://reg_40]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\Windows\\CurrentVersion\\Explorer\\ShellIconOverlayIdentifiers\\?.*
[WinRegMon://reg_41]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\Windows\\CurrentVersion\\Explorer\\ShellServiceObjects\\?.*
[WinRegMon://reg_42]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\Windows\\CurrentVersion\\Explorer\\TBDEn\\?.*
[WinRegMon://reg_43]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\Windows\\CurrentVersion\\Group Policy\\Scripts\\?.*
[WinRegMon://reg_44]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\Windows\\CurrentVersion\\Policies\\Explorer\\Run\\?.*
[WinRegMon://reg_45]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\Windows\\CurrentVersion\\Policies\\System\\?.*
[WinRegMon://reg_46]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\Windows\\CurrentVersion\\Run\\?.*
[WinRegMon://reg_47]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\Windows\\CurrentVersion\\RunOnce\\?.*
[WinRegMon://reg_48]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\Windows\\CurrentVersion\\Shell Extensions\\?.*
[WinRegMon://reg_49]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\Windows NT\\CurrentVersion\\Accessibility\\ATs\\[^\\]*
[WinRegMon://reg_50]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\Windows NT\\CurrentVersion\\AeDebug\\?.*
[WinRegMon://reg_51]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\Windows NT\\CurrentVersion\\Drivers32\\?.*
[WinRegMon://reg_52]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\Windows NT\\CurrentVersion\\Font Drivers\\?.*
[WinRegMon://reg_53]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\Windows NT\\CurrentVersion\\Image File Execution Options\\?.*
[WinRegMon://reg_54]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\Windows NT\\CurrentVersion\\SilentProcessExit\\?.*
[WinRegMon://reg_55]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\Windows NT\\CurrentVersion\\SystemRestore\\?.*
[WinRegMon://reg_56]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\Windows NT\\CurrentVersion\\Windows\\?.*
[WinRegMon://reg_57]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\Windows NT\\CurrentVersion\\Windows\\IconServiceLib\\?.*
[WinRegMon://reg_58]
hive=\\REGISTRY\\MACHINE\\Software\\Microsoft\\Windows NT\\CurrentVersion\\Winlogon\\?.*
[WinRegMon://reg_59]
hive=\\REGISTRY\\MACHINE\\Software\\Policies\\Microsoft\\PowerShell\\?.*
[WinRegMon://reg_60]
hive=\\REGISTRY\\MACHINE\\Software\\Policies\\Microsoft\\SystemCertificates\\AuthRoot\\Certificates\\?.*
[WinRegMon://reg_61]
hive=\\REGISTRY\\MACHINE\\Software\\Policies\\Microsoft\\SystemCertificates\\CA\\Certificates\\?.*
[WinRegMon://reg_62]
hive=\\REGISTRY\\MACHINE\\Software\\Policies\\Microsoft\\SystemCertificates\\Root\\Certificates\\?.*
[WinRegMon://reg_63]
hive=\\REGISTRY\\MACHINE\\Software\\Policies\\Microsoft\\Windows\\System\\Scripts\\?.*
[WinRegMon://reg_64]
hive=\\REGISTRY\\MACHINE\\Software\\Wow6432Node\\Classes\\AllFileSystemObjects\\ShellEx\\?.*
[WinRegMon://reg_65]
hive=\\REGISTRY\\MACHINE\\Software\\Wow6432Node\\Classes\\CLSID\\\{083863F1-70DE-11d0-BD40-00A0C911CE86\}\\Instance\\?.*
[WinRegMon://reg_66]
hive=\\REGISTRY\\MACHINE\\Software\\Wow6432Node\\Classes\\Directory\\ShellEx\\?.*
[WinRegMon://reg_67]
hive=\\REGISTRY\\MACHINE\\Software\\Wow6432Node\\Classes\\Folder\\ShellEx\\?.*
[WinRegMon://reg_68]
hive=\\REGISTRY\\MACHINE\\Software\\Wow6432Node\\Classes\\.*\\ShellEx\\?.*
[WinRegMon://reg_69]
hive=\\REGISTRY\\MACHINE\\Software\\Wow6432Node\\Microsoft\\Active Setup\\Installed Components\\?.*
[WinRegMon://reg_70]
hive=\\REGISTRY\\MACHINE\\SOFTWARE\\WOW6432Node\\Microsoft\\Cryptography\\OID\\?.*
[WinRegMon://reg_71]
hive=\\REGISTRY\\MACHINE\\Software\\Wow6432Node\\Microsoft\\Cryptography\\OID\\EncodingType 0\\CryptSIPDllGetSignedDataMsg\\\{SIP Guid\}\\?.*
[WinRegMon://reg_72]
hive=\\REGISTRY\\MACHINE\\Software\\Wow6432Node\\Microsoft\\Cryptography\\OID\\EncodingType 0\\CryptSIPDllVerifyIndirectData\\\{SIP Guid\}\\?.*
[WinRegMon://reg_73]
hive=\\REGISTRY\\MACHINE\\SOFTWARE\\WOW6432Node\\Microsoft\\Cryptography\\Providers\\Trust\\?.*
[WinRegMon://reg_74]
hive=\\REGISTRY\\MACHINE\\Software\\Wow6432Node\\Microsoft\\Cryptography\\Providers\\Trust\\FinalPolicy\\\{SIP Guid\}\\?.*
[WinRegMon://reg_75]
hive=\\REGISTRY\\MACHINE\\Software\\Wow6432Node\\Microsoft\\.NETFramework\\?.*
[WinRegMon://reg_76]
hive=\\REGISTRY\\MACHINE\\Software\\Wow6432Node\\Microsoft\\Office\\Excel\\Addins\\?.*
[WinRegMon://reg_77]
hive=\\REGISTRY\\MACHINE\\Software\\Wow6432Node\\Microsoft\\Office\\Outlook\\Addins\\?.*
[WinRegMon://reg_78]
hive=\\REGISTRY\\MACHINE\\Software\\Wow6432Node\\Microsoft\\Office\\PowerPoint\\Addins\\?.*
[WinRegMon://reg_79]
hive=\\REGISTRY\\MACHINE\\Software\\Wow6432Node\\Microsoft\\Office\\Word\\Addins\\?.*
[WinRegMon://reg_80]
hive=\\REGISTRY\\MACHINE\\Software\\Wow6432Node\\Microsoft\\Windows\\CurrentVersion\\Explorer\\ShellExecuteHooks\\?.*
[WinRegMon://reg_81]
hive=\\REGISTRY\\MACHINE\\Software\\Wow6432Node\\Microsoft\\Windows\\CurrentVersion\\Explorer\\ShellIconOverlayIdentifiers\\?.*
[WinRegMon://reg_82]
hive=\\REGISTRY\\MACHINE\\Software\\Wow6432Node\\Microsoft\\Windows\\CurrentVersion\\Explorer\\ShellServiceObjects\\?.*
[WinRegMon://reg_83]
hive=\\REGISTRY\\MACHINE\\Software\\Wow6432Node\\Microsoft\\Windows\\CurrentVersion\\Run\\?.*
[WinRegMon://reg_84]
hive=\\REGISTRY\\MACHINE\\Software\\Wow6432Node\\Microsoft\\Windows\\CurrentVersion\\RunOnce\\?.*
[WinRegMon://reg_85]
hive=\\REGISTRY\\MACHINE\\Software\\Wow6432Node\\Microsoft\\Windows NT\\CurrentVersion\\AeDebug\\?.*
[WinRegMon://reg_86]
hive=\\REGISTRY\\MACHINE\\Software\\Wow6432Node\\Microsoft\\Windows NT\\CurrentVersion\\Drivers32\\?.*
[WinRegMon://reg_87]
hive=\\REGISTRY\\MACHINE\\Software\\Wow6432Node\\Microsoft\\Windows NT\\CurrentVersion\\Image File Execution Options\\?.*
[WinRegMon://reg_88]
hive=\\REGISTRY\\MACHINE\\System\\CurrentControlSet\\Control\\[^\\]*
[WinRegMon://reg_89]
hive=\\REGISTRY\\MACHINE\\System\\CurrentControlSet\\Control\\Citrix\\.*
[WinRegMon://reg_90]
hive=\\REGISTRY\\MACHINE\\System\\CurrentControlSet\\Control\\CrashControl\\?.*
[WinRegMon://reg_91]
hive=\\REGISTRY\\MACHINE\\System\\CurrentControlSet\\Control\\Lsa\\[^\\]*
[WinRegMon://reg_92]
hive=\\REGISTRY\\MACHINE\\System\\CurrentControlSet\\Control\\NetworkProvider\\Order\\?.*
[WinRegMon://reg_93]
hive=\\REGISTRY\\MACHINE\\System\\CurrentControlSet\\Control\\Print\\Monitors\\?.*
[WinRegMon://reg_94]
hive=\\REGISTRY\\MACHINE\\System\\CurrentControlSet\\Control\\SafeBoot\\[^\\]*
[WinRegMon://reg_95]
hive=\\REGISTRY\\MACHINE\\System\\CurrentControlSet\\Control\\SecurityProviders\\SecurityProviders\\[^\\]*
[WinRegMon://reg_96]
hive=\\REGISTRY\\MACHINE\\System\\CurrentControlSet\\Control\\SecurityProviders\\SecurityProviders\\WDigest\\[^\\]*
[WinRegMon://reg_97]
hive=\\REGISTRY\\MACHINE\\System\\CurrentControlSet\\Control\\Session Manager\\?.*
[WinRegMon://reg_98]
hive=\\REGISTRY\\MACHINE\\System\\CurrentControlSet\\Control\\Session Manager\\AppCertDlls\\?.*
[WinRegMon://reg_99]
hive=\\REGISTRY\\MACHINE\\System\\CurrentControlSet\\Control\\Session Manager\\Environment\\?.*
[WinRegMon://reg_100]
hive=\\REGISTRY\\MACHINE\\System\\CurrentControlSet\\Control\\Terminal Server\\AddIns\\[^\\]*
[WinRegMon://reg_101]
hive=\\REGISTRY\\MACHINE\\System\\CurrentControlSet\\Control\\Terminal Server\\Wds\\rdpwd\\[^\\]*
[WinRegMon://reg_102]
hive=\\REGISTRY\\MACHINE\\System\\CurrentControlSet\\Control\\Terminal Server\\Wds\\rdpwd\\StartupPrograms\\?.*
[WinRegMon://reg_103]
hive=\\REGISTRY\\MACHINE\\System\\CurrentControlSet\\services\\NTDS\\?.*
[WinRegMon://reg_104]
hive=\\REGISTRY\\MACHINE\\System\\CurrentControlSet\\Services\\RemoteAccess\\?.*
[WinRegMon://reg_105]
hive=\\REGISTRY\\MACHINE\\System\\CurrentControlSet\\Services\\SysmonDrv\\?.*
[WinRegMon://reg_106]
hive=\\REGISTRY\\MACHINE\\System\\CurrentControlSet\\Services\\(?!(tcpip)|(device lock))[^\\]*$
type = create|set
[WinRegMon://reg_107]
hive=\\REGISTRY\\MACHINE\\System\\CurrentControlSet\\Services\\WinSock2\\?.*
[WinRegMon://reg_108]
hive=\\REGISTRY\\USER\\.*\\Control Panel\\Desktop\\[^\\]*
[WinRegMon://reg_109]
hive=\\REGISTRY\\USER\\\.DEFAULT\\Control Panel\\Desktop\\[^\\]*
[WinRegMon://reg_110]
hive=\\REGISTRY\\USER\\\.DEFAULT\\Software\\Microsoft\\Internet Explorer\\UrlSearchHooks\\?.*
[WinRegMon://reg_111]
hive=\\REGISTRY\\USER\\\.DEFAULT\\Software\\Microsoft\\Office\\Outlook\\Addins\\?.*
[WinRegMon://reg_112]
hive=\\REGISTRY\\USER\\\.DEFAULT\\Software\\Microsoft\\Office\\PowerPoint\\Addins\\?.*
[WinRegMon://reg_113]
hive=\\REGISTRY\\USER\\\.DEFAULT\\Software\\Microsoft\\Office\\Word\\Addins\\?.*
[WinRegMon://reg_114]
hive=\\REGISTRY\\USER\\\.DEFAULT\\Software\\Microsoft\\Windows\\CurrentVersion\\Run\\?.*
[WinRegMon://reg_115]
hive=\\REGISTRY\\USER\\\.DEFAULT\\Software\\Microsoft\\Windows\\CurrentVersion\\RunOnce\\?.*
[WinRegMon://reg_116]
hive=\\REGISTRY\\USER\\.*\\Environment\\[^\\]*
[WinRegMon://reg_117]
hive=\\REGISTRY\\USER\\.*\\Software\\ALPS\\?.*
[WinRegMon://reg_118]
hive=\\REGISTRY\\USER\\.*\\Software\\Classes\\CLSID\\?.*
[WinRegMon://reg_119]
hive=\\REGISTRY\\USER\\.*\\Software\\Classes\\exefile\\shell\\runas\\command\\isolatedCommand\\?.*
[WinRegMon://reg_120]
hive=\\REGISTRY\\USER\\.*\\Software\\Classes\\mscfile\\shell\\open\\command\\?.*
[WinRegMon://reg_121]
hive=\\REGISTRY\\USER\\.*\\Software\\Classes\\Wow6432Node\\CLSID\\\{BCDE0395-E52F-467C-8E3D-C4579291692E\}\\?.*
[WinRegMon://reg_122]
hive=\\REGISTRY\\USER\\.*\\Software\\Microsoft\\CTF\\?.*
[WinRegMon://reg_123]
hive=\\REGISTRY\\USER\\.*\\Software\\Microsoft\\Internet Explorer\\UrlSearchHooks\\?.*
[WinRegMon://reg_124]
hive=\\REGISTRY\\USER\\.*\\Software\\Microsoft\\MultiMedia\\?.*
[WinRegMon://reg_125]
hive=\\REGISTRY\\USER\\.*\\Software\\Microsoft\\Office\\Outlook\\Addins\\?.*
[WinRegMon://reg_126]
hive=\\REGISTRY\\USER\\.*\\Software\\Microsoft\\Office\\PowerPoint\\Addins\\?.*
[WinRegMon://reg_127]
hive=\\REGISTRY\\USER\\.*\\Software\\Microsoft\\Office Test\\?.*
[WinRegMon://reg_128]
hive=\\REGISTRY\\USER\\.*\\Software\\Microsoft\\Office\\.*\\Word\\?.*
[WinRegMon://reg_129]
hive=\\REGISTRY\\USER\\.*\\Software\\Microsoft\\Office\\Word\\Addins\\?.*
[WinRegMon://reg_130]
hive=\\REGISTRY\\USER\\.*\\Software\\Microsoft\\SystemCertificates\\AuthRoot\\Certificates\\?.*
[WinRegMon://reg_131]
hive=\\REGISTRY\\USER\\.*\\Software\\Microsoft\\SystemCertificates\\CA\\Certificates\\?.*
[WinRegMon://reg_132]
hive=\\REGISTRY\\USER\\.*\\Software\\Microsoft\\SystemCertificates\\Root\\Certificates\\?.*
[WinRegMon://reg_133]
hive=\\REGISTRY\\USER\\.*\\Software\\Microsoft\\Windows\\CurrentVersion\\App Paths\\?.*
[WinRegMon://reg_134]
hive=\\REGISTRY\\USER\\.*\\Software\\Microsoft\\Windows\\CurrentVersion\\Control Panel\\Cpls\\?.*
[WinRegMon://reg_135]
hive=\\REGISTRY\\USER\\.*\\Software\\Microsoft\\Windows\\CurrentVersion\\Explorer\\?.*
[WinRegMon://reg_136]
hive=\\REGISTRY\\USER\\.*\\Software\\Microsoft\\Windows\\CurrentVersion\\Explorer\\FileExts\\?.*
[WinRegMon://reg_137]
hive=\\REGISTRY\\USER\\.*\\Software\\Microsoft\\Windows\\CurrentVersion\\Policies\\Explorer\\Run\\?.*
[WinRegMon://reg_138]
hive=\\REGISTRY\\USER\\.*\\Software\\Microsoft\\Windows\\CurrentVersion\\Run\\?.*
[WinRegMon://reg_139]
hive=\\REGISTRY\\USER\\.*\\Software\\Microsoft\\Windows\\CurrentVersion\\RunOnce\\?.*
[WinRegMon://reg_140]
hive=\\REGISTRY\\USER\\.*\\Software\\Microsoft\\Windows\\CurrentVersion\\Shell Extensions\\Cached\\?.*
[WinRegMon://reg_141]
hive=\\REGISTRY\\USER\\.*\\Software\\Microsoft\\Windows NT\\CurrentVersion\\Accessibility\\[^\\]*
[WinRegMon://reg_142]
hive=\\REGISTRY\\USER\\.*\\Software\\Microsoft\\Windows NT\\CurrentVersion\\AppCompatFlags\\Compatibility Assistant\\?.*
[WinRegMon://reg_143]
hive=\\REGISTRY\\USER\\.*\\Software\\Nico Mak Computing\\?.*
[WinRegMon://reg_144]
hive=\\REGISTRY\\USER\\.*\\Software\\Policies\\Microsoft\\SystemCertificates\\AuthRoot\\Certificates\\?.*
[WinRegMon://reg_145]
hive=\\REGISTRY\\USER\\.*\\Software\\Policies\\Microsoft\\SystemCertificates\\CA\\Certificates\\?.*
[WinRegMon://reg_146]
hive=\\REGISTRY\\USER\\.*\\Software\\Policies\\Microsoft\\SystemCertificates\\Root\\Certificates\\?.*
[WinRegMon://reg_147]
hive=\\REGISTRY\\USER\\.*\\Software\\Policies\\Microsoft\\Windows\\System\\Scripts\\?.*
[WinRegMon://reg_148]
hive=\\REGISTRY\\USER\\.*\\Software\\Synaptics\\?.*
[WinRegMon://reg_149]
hive=\\REGISTRY\\USER\\.*\\Software\\WinRAR\\?.*
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

## server.conf
Make sure to set the hostname:
```ini
[general]
serverName = CNC_WinServ2016_10.0.23.141
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