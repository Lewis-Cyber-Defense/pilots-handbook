---
title: OS Info
description: 
published: true
date: 2024-02-23T14:40:39.798Z
tags: 
editor: markdown
dateCreated: 2024-02-22T06:13:44.259Z
---

# Checking OS Version
There are two ways to check OS version:
- `lsb_release -a`
- `cat /etc/os-release`
# Passwords
Passwords can be changed with the `passwd` command:

| command | description |
| ---- | ---- |
| `passwd` | changes current user password |
| `passwd <user>` | changes specified user password |
| `passwd -d <user>` | allows passwordless logins by deleting user password |
| `passwd -l <user>` | locks user account and prevents all logins. <br>- Someone could probably write a bash oneliner that disables logins for all users except root and sysadmin  |

Password ***hashes*** are stored in `/etc/shadow`
 - **There should be no passwords in this file, only hashes for users who login**

Example entry for a login user:

`sysadmin:$y$j9T$BgZdPus8LQnCRO5rSCN0f1$Urv3EDlCMwOpyJ3qYw4aasDCrGi6refPNJU6RhoIYA3:19673:0:99999:7:::`

Example entry for a non-login user:

`rpc:!*:19450::::::`

Vulnerable information leak example:

`root:changeme:19761::::::`
# Users
Users can be shown by reading `/etc/passwd`

Alternatively, run `cat /etc/passwd | cut -d ':' -f 1` to get a list of just usernames.

The `/etc/passwd` file contains the following format:
`username:password:UID:GID:comment:home:shell`

Note that the password should either be `x` or a hash, not an actual password.

Users can be deleted by simply removing the line in `/etc/passwd` or by running `userdel -r <user>`

**Only login users should have a shell**, users without login will have `<path>/nologin` as the shell. It wouldn't be dumb to check the nologin binary (or attempt user login) to verify that it works as intended.

Note that all users should have a unique ID, and **UID 0 IS RESERVED FOR ROOT** (no other users should have a uid or gid of 0)
# Groups
Groups are located in `/etc/group`, with the following format:
`groupname:password:GID:users`

The most important groups are the `root` and `wheel` groups, which contain users allowed to run sudo commands.
# Sudo
Sudo allows specific users to execute commands as the root user.

These users are determined in the `/etc/sudoers` file and all files in the `/etc/sudoers.d/` directory.

These files allow users or groups to execute a command, and can even prevent password requirements for certain commands.

Permissions can also be seen with `sudo -l`
# Updates and Installs
Updates can be done easily based on the OS

Debian-based systems:
```shell
sudo apt-get update && sudo apt-get upgrade
```

Red hat-based systems:
```shell
sudo yum update
sudo yum upgrade
```

Arch-based systems:
```shell
sudo pacman -Syu
```

Installs are done similarly:
```shell
sudo apt-get install <package>    #debian

sudo yum install <package>        #red hat

sudo pacman -S <package>          #arch
```
# Binaries
Binary files are located in a few directories, usually seen in PATH.

To see the path, run `echo $PATH`

Here are some common binary locations:
```shell
/usr/local/bin
/usr/local/sbin
/usr/bin
/usr/sbin
/bin
/sbin
```
Directories can be added to PATH with `export PATH="<path>:$PATH"`
# Crontab
The crontab allows for scheduled tasks.

Running `crontab -l` shows the current user's crontab entries (located in `/var/spool/cron/crontabs`).

`crontab -r` removes the current user's crontab entries.

System crontabs can be seen with `ls -la /etc/cron*`

The following oneliner displays the crontabs of all users:
`for user in $(cut -f1 -d: /etc/passwd); do echo $user; crontab -u $user -l; done`

Crontabs are executed by the cron daemon (crond), which can be stopped with `systemctl mask cron`

Crond searches `/var/spool/cron`, `/etc/cron.d/`, and `/etc/crontab` for tasks to run.
# Misc
## .bashrc
The `~/.bashrc` file determines how shell sessions will work based on the user.

Command history can be enabled with the following lines in .bashrc:
```bash
set -o history
HISTFILE=$HOME/.bash_history
```

Basically every line in this file is executed from top down at the start of each shell session.
## /tmp
`/tmp` is a directory of temporary files. All users have read and write access to files in this directory, which is frequently used to store malware.

Files are deleted from here on a reboot.
## env
The `env` command displays environment variables.

Information might be leaked here, especially in docker containers.
## SUID/SGID
SUID and SGID run an executable with the permissions of the user owner and group owner respectively.

These files can be found with the following commands:
```bash
find / -type f -perm -4000 ! -path "/proc/*" -exec ls {} 2</dev/null \; # SUID

find / -type f -perm -2000 ! -path "/proc/*" -exec ls {} 2</dev/null \; # SGID
```

These binaries can be checked using [GTFOBins](https://gtfobins.github.io/#+suid) (make sure you type the binary file before the `+suid`)
## World-writable files
World writable files could possibly be used to store malware or to export data

Find these with the following:
```bash
find / -type f -perm -2 ! -path "/proc/*" -exec ls -l {} 2</dev/null \;
```
## Filename whitespace detector
Malicious files can be created that look identical to files when using `ls` or other listings, using the following procedure:
```bash
> touch 'index.php'
> touch $'index\u200D.php'
> ls
index.php  index.php
> ls -la
total 2
-rw-r--r--  1 sysadmin sysadmin   0 Feb 23 08:33 index.php
-rw-r--r--  1 sysadmin sysadmin   0 Feb 23 08:33 index.php
```
To protect against this, I wrote the following python script:
```py
#!/usr/bin/env python3

import os

def contains_non_printable(file_path):
    try:
        return any(ord(char) > 127 for char in file_path)
    except TypeError:
        return False


for root, dirs, files in os.walk('/'):
    for file in files:
        if contains_non_printable(file):
            full_path = os.path.join(root, file)
            print(f"Found file with non-printable characters: {full_path}")
```