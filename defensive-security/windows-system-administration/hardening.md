---
title: Hardening
description: 
published: true
date: 2024-02-24T05:16:30.337Z
tags: 
editor: markdown
dateCreated: 2024-02-22T06:13:53.944Z
---

# Hardening
- https://github.com/PaulSec/awesome-windows-domain-hardening (a general guide)

# Stopping and Disabling Services (ez)
- https://www.windowscentral.com/how-start-and-stop-services-windows-10
- Open services > stop service > disable service > ????? > profi

# Windows Hardening Scripts
- Bypass digital signature
```
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
```
- **Active Directory Hardening (2019):**
	- https://github.com/LoicVeirman/SecureAD (source)
		- You need to download the whole repository in order for HardenAD.ps1 to run correctly
			- https://github.com/LoicVeirman/SecureAD/blob/Master/HardenAD.ps1 (ps file) (try this second)
	- **If that doesn't work, try:** 
		- https://github.com/atlantsecurity/windows-hardening-scripts/blob/main/windows-server-2019-hardening-script.cmd
	- **Other stuff you can try that didn't work for me:** 
		- https://raw.githubusercontent.com/Cloudneeti/os-harderning-scripts/master/WindowsServer2019/CSBP_WindowsServer2019.ps1 (didn't work when I tried)
		- https://raw.githubusercontent.com/Cloudneeti/os-harderning-scripts/master/WindowsServer2019/CIS_Benchmark_WindowsServer2019_v100.ps1  (didn't work when I tried)
		- https://help.zscaler.com/zcspm/windows-server-2019 (kind of worked but I don't think it did anything; you also have to install the four tools below)
```
Install-Module -Name AuditPolicyDsc
```
```
Install-Module -Name SecurityPolicyDsc
```
```
Install-Module -Name NetworkingDsc
```
```
Install-Module -Name PSDesiredStateConfiguration
```
- Windows 10 Hardening: 
	- https://gist.github.com/mackwage/08604751462126599d7e52f233490efe (I have not tested this but pretty sure it works)

# Incident Response Scripts
- PowerShell Incident Response Windows Cheat Sheet (ps file): 
	- https://gist.github.com/RomelSan/9ebef17aa9aa061d6b32e2e250181942

# More on scripts
- You can find any script online! Just make sure it's safe and test it on a practice environment first! 
- To run a script: 
```
./program_name
```
	- or just type the program name out

# Active Directory Hardening (On top of running scripts)
- To get into Group Policy Management Editor 
	- Domains > Default Domain Policy > Right Click > Edit
		- This will bring you to the editor
	- Main pathways: 
		- `Computer Configuration` -> `Policies` -> `Administrative Templates` 
		- `Computer Configuration` -> `Windows Settings -> Security Settings 
- Enable do not store LAN Manager hash value
	- `Security Settings > Local Policies > Security Options > double click Network security - Do not store LM hash value on next password change policy > select "Define policy setting" > Enabled` 
-  Changing remote desktop settings
	- `Administrative Templates` -> `Windows Components` -> `Remote Desktop Services` -> `Remote Desktop Session Host` -> `Security`
- Disable Guest Accounts
- Set Audit Policies
	- `Security Settings > Local Policies > Audit Policy` 
- Set User Rights Assignments 
	- `Security Settings > Local Policies > User Rights Assignment 
- Set Security Options
	- `Security Settings > Local Policies > Security Options 
- Enable SMB Signing
	- `Security Settings > Local Policies > Security Options > double click Microsoft network server: Digitally sign communication (always) > select Enable`
		- enabled this comp day
- Enable LDAP Signing 
	- `Security Settings > Local Policies > Security Options > Domain controller: LDAP server signing requirements > select Require`
		- enabled this
- Account Policies (Password, Account Lockout, Kerberos)
	- `Windows Settings > Security Settings > Account Policies
		- Password and Lockout seem most important
