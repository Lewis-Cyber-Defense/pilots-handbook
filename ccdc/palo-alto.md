---
title: Palo Alto Firewall
description: 
published: true
date: 2025-01-30T20:13:24.711Z
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

## Temporarily Turn Off Management Interface 

You don't know who is accessing it. 

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
Red Team Could Be Managing the FW Via Data Interface

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

















