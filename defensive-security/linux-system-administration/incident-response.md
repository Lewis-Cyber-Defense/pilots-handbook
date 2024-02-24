---
title: Incident Response
description: 
published: true
date: 2024-02-22T23:06:40.302Z
tags: 
editor: markdown
dateCreated: 2024-02-22T06:13:34.669Z
---

# Network Connections
Note: run any `ss` commands as root or with sudo to see process information

See all network connections (useful for established sessions):
```bash
ss -tupan
```
See all listening ports/processes (useful for reverse shell listeners):
```bash
ss -tulpn
```
Get process ID easily for processes listening on ports (useful to condense output):
```bash
sudo ss -nlp | grep -E 'LISTEN.*(([0-9]+\.){3}[0-9]+|\*|):[0-9]+' | sed -e 's/ \ \ /\ /g' | grep -e 'pid=[0-9]*'
```

# Processes
List all processes (useful to see process tree and other information):
```bash
ps faxo user,uid,pid,ppid,tt,start,exe,command
```
Note: remove the `exe` part to get a more condensed output

If you don't want to type as much but also want less useful information:
```bash
ps aux
```

# Log analysis


## ChopChopGo

* Utilizes Sigma male :) rules for forensics artifact recovery. 
* Looks through logs, and finds what  went wrong and where

Tested on: 

Ubuntu 22.04 Server



**Installation**:

```
git clone https://github.com/M00NLIG7/ChopChopGo.git
```

**You might need development files for systemd:**

```
# Ubuntu/Debian
sudo apt-get install libsystemd-dev


# DISCLAIMER: The following are untested:

# CentOS 6 or older
sudo yum install systemd-devel

# CentOS 7 or newer
sudo dnf install systemd-devel

# Fedora 
sudo dnf install systemd-devel

```

**Install Go:**

```
# Important: Don't install regular go or gccgo-go. Do the below commands:

# Ubuntu/Debian
sudo apt install golang-go

# Type in the above command, then go into the DIRECTORY ChopChopGo, then type this command in again. You should now see a file with the name ChopChopGo.



# DISCLAIMER: The following are untested:

# Fedora
sudo dnf install epel-release 
sudo dnf install golang

# CentOS
sudo yum install epel-release 
sudo yum install golang
```

**Update Sigma Rules:**

```
./update-rules.sh
```


**Command Examples:**

```
# Defaults to searching through syslog

./ChopChopGo 

# This searches through auditd log with the official sigma rules:
# (I assume that /opt/evidence/auditd.log can be replaced with whatever directory is on your specific machine.)

./ChopChopGo -target auditd -rules ./rules/linux/auditd/ -file /opt/evidence/auditd.log

# This searches through journald with specified rules:

./ChopChopGo -target journald -rules ./rules/linux/builtin/

# Output in CSV format:
	
	Add to end of command:

		"-out csv"


# Output in JSON format:

	Add to end of command:
		
		"-out json" 

```





