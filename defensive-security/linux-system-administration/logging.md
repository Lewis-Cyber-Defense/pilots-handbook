---
title: Logging
description: 
published: true
date: 2024-02-22T15:49:28.106Z
tags: 
editor: markdown
dateCreated: 2024-02-22T06:13:40.961Z
---

Auditd logging
```bash
grep -E 'EXECVE' -B 1 -A 3 /var/log/audit/audit.log > ~/command_executions; sed -i -e 's/^type=SYSCALL/\nSyscall/' -e 's/^type=EXECVE/Command/' -e 's/^type=CWD/Current_directory/' -e 's/^type=PATH/Path/' ~/command_executions; awk '{match($0, /msg=audit\(([0-9]+)\.[0-9]+:[0-9]+\)/, a); $0=gensub(/msg=audit\([0-9]+\.[0-9]+:[0-9]+\)/, strftime("%H:%M:%S", a[1]), "g"); print}' ~/command_executions > ~/command_executions2; mv ~/command_executions2 ~/command_executions; sed -i -e 's/arch=.* success/success/' -e 's/exit=.* ppid/ppid/' -e 's/ comm=/ command=/' -e 's/ cwd=//' -e 's/inode.*ouid/ouid/' -e 's/ rdev=.*//' -e 's/argc=/arguments=/' ~/command_executions
```