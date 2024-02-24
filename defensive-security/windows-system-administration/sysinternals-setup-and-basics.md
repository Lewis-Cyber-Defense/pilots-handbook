---
title: sysinternals-setup-and-basics
description: 
published: true
date: 2024-02-24T05:16:35.847Z
tags: 
editor: markdown
dateCreated: 2024-02-24T01:57:38.161Z
---

# Sysinternals Download and Setup
- Download from URL 
	- https://learn.microsoft.com/en-us/sysinternals/downloads/sysinternals-suite
- Or download from command line (I haven't tried these commands to see if they work):
```
Download-SysInternalsTools C:\Sysinternals
```
	OR 
```
Install-Module -Name SysInternals 
```
- **After download, you can launch tools from command line just by typing the program name in (elevated) PowerShell or Command Prompt** (Need to edit environment variables):
	- Type "sysdm.cpl" in command prompt
	- click on 'Advanced' Tab
	- Click 'Path' > 'Edit'
	- 'New'
	- Paste file path of sysinternals suite (desktop)
	- Now you can type the following commands in an **elevated **PS or cmd window: 
		- tcpview
		- procexp
		- procmon
		- sysmon

## Sysmon Setup
- **Configuration file for Sysmon**:  
	-  https://github.com/olafhartong/sysmon-modular (has different config files but we will use the one below; also includes more info and instructions)
	- Configuration file we will use: https://raw.githubusercontent.com/olafhartong/sysmon-modular/master/sysmonconfig-with-filedelete.xml 
	- An alternative configuration file but you should use the one from above: 
		- https://github.com/SwiftOnSecurity/sysmon-config
- Installing sysmon with config file: 
```
 sysmon.exe -accepteula -i sysmonconfig.xml
```
- Update existing configuration file
```
sysmon.exe -c sysmonconfig.xml
```
- If installed correctly, 'sysmon' should be added to **Event Viewer**
- To view sysmon logs, open Event Viewer: 
	- > Application and Service Logs > Microsoft > Windows > Sysmon > Operational

(Might remove this small section?)
- Executing sysmon binary:
```
Sysmon.exe -accepteula -i ..\Configuration\swift.xml`
```
	- path of where the download is

## More about Sysinternals (Instructions in previous section)
- Similar to Windows Event Logs but with more detail and granular control
- The location of all executables is 
	- C:\\Windows\\System32
- Four main tools: 
	- tcpview
	- procexp
	- procmon
	- sysmon
- Extra tools: 
	- OSQuery #tools 
		- open-source tool
		- OSQuery only allows querying of events inside the machine*
		- queries endpoints using SQL syntax
		- #command: osqueri 
			- try it in PowerShell
		- Example of listing important process information by its process name: 
	```
	select pid,name,path from processes where name='lsass.exe';
	```
	-  Autoruns
		- Good tool for searching malicious entries created int he local machine to establish persistence
		- Program reports explorer shell extensions, browser helper objects, winlogon notifications, auto-start services, etc. 
		- has a 'image hijacks' section which may be useful
		- **This tool was very helpful on competition day for MWCCDC**