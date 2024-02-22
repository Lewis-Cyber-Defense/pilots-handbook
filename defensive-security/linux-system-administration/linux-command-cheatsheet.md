---
title: linux-command-cheatsheet
description: 
published: true
date: 2024-02-22T15:49:25.805Z
tags: 
editor: markdown
dateCreated: 2024-02-22T06:13:37.767Z
---

# Linux Fundamentals  

## Basic Commands
```bash  
	echo - basically print  
	man - gives full manual page for any given command  
	cd - changes directory  
	ls - lists all files/directories in a directory (use -a to show hidden files)  
	cat - outputs contents of file to console  
	touch - creates files  
	su - changes user  
	ssh - allows for remote CLI access to other machines
	scp - allows copying of files to remote machines 
```

## Linux Operators
```bash
	&& - executes a second command after the first has completed successfully
	& - allows you to keep using commands while others are running (background the process)
	$ - denotes an enviornment variable (export <varname>=<value> will set a variable)  
	| - pipes output to another command  
	; - like &&, but does not require first to run successfully
	> - redirects output of command to a file
	>> - appends output of a command to a file  
```

## File Management  
```bash
	chown <user>:<group> file - changes ownership of a file  
	chmod <ugo> file - sets read(4), write(2), and execute(1) permissions  
	rm - removes a file  
	mv - moves a file  
	mkdir/rmdir - makes and removes a directory, respectively  
	ln - creates a symbolic link between files 
	find - finds files  
	grep - searches within files for given input  
```

## User Management  
```bash
	sudo <command> - runs command with escalated permissions  
	adduser - adds user
	userdel - deletes a user account
	addgroup - adds group
	delgroup - deletes a group
	usermod -a -G <groups seperated by commas> <user> - modifies what groups a user is in  
```

## Networking
```shell
	nc <ip> <port> - connect to ip address on given port
	nc -lvnp <port> - start a listener on a given port
	ping <ip> - check if a host is up using ICMP
	curl <url> - access websites from terminal
	wget <url> - download files
```

## Devices
```shell
	lsblk - lists block devices
	lsusb - lists usb devices
	lsof - lists opened files
	lspci - lists pci devices
```

## Basic Shell Scripting 
1. Store bash commands in a file with a .sh extension  
```bash
echo hello
echo whoami
whoami
```
2. That file can be run using bash `<file>`  
```bash
> bash file.sh
hello
whoami
<user>
```
3. You don't have to put a .sh if you use a shebang at the top of the file  
```bash
#!/bin/bash
echo hello
echo whoami
whoami
```

## Important Files and Directories  
- `/etc/passwd` -> stores user information, shows all users on a system  
- `/etc/shadow` -> stores all of the passwords (hashed, obviously)  
- `/tmp` -> every file inside will get deleted upon shutdown  
- `/etc/sudoers` -> controls sudo permissions of every user on system  
- `/home` -> where all of your stuff is  
- `/root` -> root user's home directory  
- `/usr` -> where all software is installed  
- `/bin` and `/sbin` -> contains basic programs for linux to function  
- `/var` -> linux's home for misfit data  
- `$PATH` -> contains all of the binaries you can execute
