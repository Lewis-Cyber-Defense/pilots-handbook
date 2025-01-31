---
title: Palo Alto Firewall
description: 
published: true
date: 2025-01-31T19:22:09.892Z
tags: 
editor: markdown
dateCreated: 2024-02-22T06:12:33.415Z
---

# Palo Alto FW Cheatsheet

# VM-100 Firewall

## Quick Start

**Default Creds**

- User: admin
- Pass: admin

### Things to Note
Configuration mode is denoted with the `#` symbol before the command. 
To exit config mode, type in `exit`.
## Toggle the Management Interface (GUI)

Sets the Firewall to config mode
`configure` 

Set access to local only (I think)
`set deviceconfig system permitted-ip 127.0.0.1`

`commit`

To turn it back on:

in config mode:
`set deviceconfig system permitted-ip x.x.x.x`

`commit`

## Temporarily Turn Off Data External Interface (if connected)
Red Team Could Be Managing the Firewall Via Data Interface

In configure mode: 
`set network interface ethernet ethernet1/x link-state
down`

`commit`

To turn it back on: 

in config mode:
`set network interface ethernet ethernet1/1 link-state up`

`commit`

## Change Passwords

**For the admin password:**

In config mode:
`set mgt-confg users admin password`


## If necessary, disable dhcp-client if you want static IPs 
Necessary if you want to do static IPs 

`show system info`

Check next to *ip-assignment:*  If it says "dhcp", do:

`set deviceconfig system type static`

## Check and Disallow Protocols to Firewall

`show system services`

In config mode: 

To see what services can be disabled: 
`set deviceconfig system service disable-`

To disable a specific service:
`set deviceconfig system service disable-[SERVICE NAME] no`

`commit`

## Admin accounts

Note: admin and panorama are default users 

**Change admin password**

In config mode:
`set mgt-confg users admin password`

**Check for additional admin accounts**

`show admins all`

**Delete admin accounts**

config mode:

`delete mgt-config users [SUS ACCOUNT NAME HERE]`

`commit`

## Creating Snapshots

**NOTE: For your own sanity, do this *after* you license and fully config the firewall.**

In operations mode:

`scp export configuration to username@host:/home/secops from running-config.xml`


## License Activation

### Method 1: GUI

Top Bar: Device > Under License Managemenet > Activate feature using authorization code

Enter the code


### Method 2: CLI
If we have the License Code (untested): 
`request license activate code`

## Update Malware Signatures
Top Bar: Device > Dynamic Updates > check now

Click around, and download stuff

## Custom Security Profiles

### Anti-Virus

**ACTION TAB**
- For North-South traffic, set actions to "drop"

- For East-West, set actions to "reset-both"

**WILDFIRE INLINE ML TAB**
- Enable Inline Machine Learning

### Anti-Spyware

- For North-South traffic, set actions to drop:tical and hirything else 





## TODO LIST

1. How to enable / disable GUI
2. How to install malware signatures
3. Write about deployment types 
4. How to use GUI
5. Better practices 
6. What to do if we lose control 
7. How to actually import the snapshot .xml. 
















