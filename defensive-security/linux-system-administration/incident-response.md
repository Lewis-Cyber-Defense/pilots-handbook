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
