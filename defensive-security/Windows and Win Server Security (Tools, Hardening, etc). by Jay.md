---
title: Windows System Administration
description: 
published: true
date: 2024-02-23T19:43:49.571Z
tags: 
editor: markdown
dateCreated: 2024-02-23T06:11:51.710Z
---

# 🪟 Windows System Administration
- Note from Jay (2/23/24): 
	- **Going to copy and paste my notes here for now. Will (maybe) fix after the competition**
	- This is for Windows OS **and** Windows Servers OS, but leans toward Servers (Be mindful of scripts for each respective OS)

-  **Log files reside in**: (windows event viewer)
	 ```
	C:\Windows\System32\winevt\Logs
	```
- Event viewer > Windows Logs > System
	- view tabs
- Sysinternals (tools for monitorin)
	- TCPView #tool
		- shows TCP and UDP endpoints on system
			- includes local and remote addresses
			- TCP connection states
	- Process Explorer #tool
		- has two windows
			- top window: 
				- shows list of currently active processes 
				- names of owning accounts, 
			- bottom window: 
				- different modes
					- handle mode: view handles taht the process selected in the top window has opened
					- DLL mode: view DLLs and memory-mapped files that process has loaded

# Hardening Reference
- https://github.com/PaulSec/awesome-windows-domain-hardening

# Stopping and Disabling Services (ez)
- https://www.windowscentral.com/how-start-and-stop-services-windows-10
- Open services > stop service > disable service > ????? > profit

# Sysinternals Download and Setup (DONE)
- Download from URL 
	- https://learn.microsoft.com/en-us/sysinternals/downloads/sysinternals-suite
- Or download from command line:
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
## TCPView (Sysinternals Tool)
- shows detailed list of TCP and UDP endpoints 
	- includes local and remote addresses; state of TCP connections
- Tcpvcon is the command line version of TCPView
- "Resource monitor" (resmon) is a good alternative already installed on Windows by default
- top includes filters
	- TCPv4, TCPv6, UDPv4, UDPv6
		- blue upon click means setting is chosen (UDP traffic might not show unless UDP traffic)
	- Green flag = states filter 
		- closed, listen, established, closing, ack, etc
```
tcpview
```

## Process Explorer (Sysinternals Tool)
- to inspect the agent process, its properties, and associated threads and handles.
- Shows active processes with name of owning accounts (top window)
	- bottom window shows different output depending on mode: 
		- handle mode: see handles of selected process
		- DLL mode: show DLLs and memory-mapped files that process has loaded
- Right-click a process to create a 'Minidump' or 'Full Dump'
	- can be interpreted to figure out the cause of a crash dump
- To analyze dump file
	- In taskbar, search 'WinDbg' > run as admin
	- File > Start Debugging > Open file you want to view
	- Select dump from folder location, ex: %SystemRoot%\\Minidump
	- Open
	- May take several minutes
	- in run command type: !analyze -v  
		- then enter key
- Right-click a process > Properties
	- Will show many tabs
	- "Services" tab
		- will show service associated with process.
		- Ex: svchost.exe process associated with Service 'WebClient'
	- "TCP/IP" tab
		- shows web traffic
		- you can use "whois" to find info of remote address #big 
- To verify image signatures ( #big):
	- Options > Verify Image Signatures
	- will show up as a column 
- Run at logon
	- Options > Run at logon 
- Colors: 
	- purple: packed file
	- red: process exiting
	- green: just loaded
	- light blue: run by same account that started process explorer
	- dark blue: process is a service (ex: svchost.exe)
	- dark grey: a process that *you* suspend is this color until unsuspended
```
procexp
```

## Process Monitor (Sysinternals Tool)
- to investigate if there were any indicators on why the agent was not operating as it should.
-  advanced monitoring: shows real-time file system, registry and process/thread activity 
	- combines features of legacy sysinternals util: filemon, regmon
- **There's going to be a shit ton of events so you'll have to set filters**
	- To pause captured events: File > 'Capture Events'
- Create a filter #big:
	- click green funnel/filter icon > pick setting (ex: PID is 3888 for notepad or PID is 2228 for file explorer) > then pick condition for "is" > make sure 'include' is set
	-  you can set a filter for the 'process name' and other options.
		- time of day
		- PID
		- operation (type of event)
		- path
		- result (SUCCESS, REPARSE, BUFFER, OVERFLOW, NAME NOT FOUND)
		- detail
	- no case sensitivity when naming
- 5 icons that will show 
	- Show Registry Activity
	- Show File System Activity
	- Show Network Activity
	- Show Process and Thread Activity
	- Show Profiling Events
```
procmon
```
## Sysmon Setup (Sysinternals Tool)
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

- Executing sysmon binary:
```
Sysmon.exe -accepteula -i ..\Configuration\swift.xml`
```
	- path of where the download is
## Sysmon Overview (Sysinternals Tool)
- Events within Sysmon are stored in 
	- `Applications and Services Logs/Microsoft/Windows/Sysmon/Operational`
-  Sysmon has 29 event IDs
	- Those of importance include 1, 3, 7, 8, 11, 12/13/14, 15, 22
- **Event ID 1: Process Creation**
	- looks for processes that have been created
	- can use to look for suspicious processes or processes with typos
	- this following command excludes svchost.exe from eventlogs
```
<RuleGroup name="" groupRelation="or">
	<ProcessCreate onmatch="exclude">
	 	<CommandLine condition="is">C:\Windows\system32\svchost.exe -k appmodel -p -s camsvc</CommandLine>
	</ProcessCreate>
</RuleGroup>
```
- **Even ID 3: Network Connection**
	- looks for events that occur remotely 
	- includes files and sources of suspicious binaries as well as open ports
	- the following code snippet includes two ways to identify suspicious network connection activity. The first way will identify files transmitted over open ports. 
		- In this case, we are specifically looking for **nmap.exe** which will then be reflected within the event logs. The second method** identifies open ports and specifically port 4444** which is commonly used with Metasploit. 
	- If the condition is met an event will be created and ideally trigger an alert for the SOC to further investigate.
```
<RuleGroup name="" groupRelation="or">
	<NetworkConnect onmatch="include">
	 	<Image condition="image">nmap.exe</Image>
	 	<DestinationPort name="Alert,Metasploit" condition="is">4444</DestinationPort>
	</NetworkConnect>
</RuleGroup>
```
- **Event ID 7: Image Loaded**
	- looks for DLLs loaded by processes
	- useful when hunting for DLL Injection and DLL Hijacking attacks
	- #Caution: this Event ID causes a high system load 
	- The following code snippet looks for DLLs that have been loaded within the \\Temp\\ directory
		- If a DLL is loaded within this directory it can be considered an anomaly
```
<RuleGroup name="" groupRelation="or">
	<ImageLoad onmatch="include">
	 	<ImageLoaded condition="contains">\Temp\</ImageLoaded>
	</ImageLoad>
</RuleGroup>
```
- **Event ID 8: CreateRemoteThread**
	- CreateRemoteThread Event ID will monitor for processes injecting code into other processes. The CreateRemoteThread function is used for legitimate tasks and applications. 
	- However, it could be used by malware to hide malicious activity. This event will use the SourceImage, TargetImage, StartAddress, and StartFunction XML tags.
	- The below code snippet shows two ways of monitoring for CreateRemoteThread. The first method will look at the memory address for a specific ending condition which could be an indicator of a Cobalt Strike beacon. The second method will look for injected processes that do not have a parent process.
```
<RuleGroup name="" groupRelation="or">
	<CreateRemoteThread onmatch="include">
	 	<StartAddress name="Alert,Cobalt Strike" condition="end with">0B80</StartAddress>
	 	<SourceImage condition="contains">\</SourceImage>
	</CreateRemoteThread>
</RuleGroup>
```
- Event ID 11: File Created
	- event ID is will log events when files are created or overwritten the endpoint. This could be used to identify file names and signatures of files that are written to disk. 
	- This event uses TargetFilename XML tags.
	- The below code snippet is an example of a ransomware event monitor.
```
<RuleGroup name="" groupRelation="or">
	<FileCreate onmatch="include">
	 	<TargetFilename name="Alert,Ransomware" condition="contains">HELP_TO_SAVE_FILES</TargetFilename>
	</FileCreate>
</RuleGroup> 
```
- Event ID 12/ 13/14: Registry Event
	- event looks for changes or modifications to the registry. Malicious activity from the registry can include persistence and credential abuse. 
	- This event uses TargetObject XML tags.
	- The below code snippet will look for registry objects that are in the _"Windows\\System\\Scripts"_ directory as this is a common directory for adversaries to place scripts to establish persistence.
```
<RuleGroup name="" groupRelation="or">
	<RegistryEvent onmatch="include">
	 	<TargetObject name="T1484" condition="contains">Windows\System\Scripts</TargetObject>
	</RegistryEvent>
</RuleGroup>
```
- Event ID 15: FileCreateStreamHash
	- This event will look for any files created in an alternate data stream. This is a common technique used by adversaries to hide malware. 
	- This event uses TargetFilename XML tags.
	- The below code snippet will look for files with the .hta extension that have been placed within an alternate data stream.
```
<RuleGroup name="" groupRelation="or">
	<FileCreateStreamHash onmatch="include">
	 	<TargetFilename condition="end with">.hta</TargetFilename>
	</FileCreateStreamHash>
</RuleGroup>
```
- Event ID 22: DNS Event
	- event will log all DNS queries and events for analysis. 
		- The most common way to deal with these events is to exclude all trusted domains that you know will be very common "noise" in your environment. 
		- Once you get rid of the noise you can then look for DNS anomalies. 
	- This event uses QueryName XML tags.
	- the below code snippet will get exclude any DNS events with the .microsoft.com query. This will get rid of the noise that you see within the environment.
```
<RuleGroup name="" groupRelation="or">
	<DnsQuery onmatch="exclude">
	 	<QueryName condition="end with">.microsoft.com</QueryName>
	</DnsQuery>
</RuleGroup> 
```

# Active Directory Hardening
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

# Windows Scripts
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
- PowerShell Incident Response Windows Cheat Sheet (ps file): 
	- https://gist.github.com/RomelSan/9ebef17aa9aa061d6b32e2e250181942
- To run a script: 
```
./program_name
```
	- or just type the program name out


# DeepBlueCLI (Extra)
- Download
	- https://github.com/sans-blue-team/DeepBlueCLI

- **Log files reside in**: (windows event viewer)
 ```
C:\Windows\System32\winevt\Logs
```
- Syntax
```
.\DeepBlue.ps1 <event log name> <evtx filename>
```
- Example command (notice .evtx extension which is located in folder above)
```
.\DeepBlue.ps1 .\evtx\smb-password-guessing-security.evtx
```
- Possibly useful file path
```
C:\Windows\System32\winevt\Logs\Security.evtx
```
- From the github: 
	- Process local Windows security event log (PowerShell must be run as Administrator):
		`.\DeepBlue.ps1` 
			or 
		`.\DeepBlue.ps1 -log security`
- Process local Windows system event log:
		`.\DeepBlue.ps1 -log system`
## Examples
| Event                                   | Command                                                                   |
| --------------------------------------- | ------------------------------------------------------------------------- |
| Event log manipulation                  | `.\DeepBlue.ps1 .\evtx\disablestop-eventlog.evtx`                         |
| Metasploit native target (security)     | `.\DeepBlue.ps1 .\evtx\metasploit-psexec-native-target-security.evtx`     |
| Metasploit native target (system)       | `.\DeepBlue.ps1 .\evtx\metasploit-psexec-native-target-system.evtx`       |
| Metasploit PowerShell target (security) | `.\DeepBlue.ps1 .\evtx\metasploit-psexec-powershell-target-security.evtx` |
| Metasploit PowerShell target (system)   | `.\DeepBlue.ps1 .\evtx\metasploit-psexec-powershell-target-system.evtx`   |
| Mimikatz `lsadump::sam`                 | `.\DeepBlue.ps1 .\evtx\mimikatz-privesc-hashdump.evtx`                    |
| New user creation                       | `.\DeepBlue.ps1 .\evtx\new-user-security.evtx`                            |
| Obfuscation (encoding)                  | `.\DeepBlue.ps1 .\evtx\Powershell-Invoke-Obfuscation-encoding-menu.evtx`  |
| Obfuscation (string)                    | `.\DeepBlue.ps1 .\evtx\Powershell-Invoke-Obfuscation-string-menu.evtx`    |
| Password guessing                       | `.\DeepBlue.ps1 .\evtx\smb-password-guessing-security.evtx`               |
| Password spraying                       | `.\DeepBlue.ps1 .\evtx\password-spray.evtx`                               |
| PowerSploit (security)                  | `.\DeepBlue.ps1 .\evtx\powersploit-security.evtx`                         |
| PowerSploit (system)                    | `.\DeepBlue.ps1 .\evtx\powersploit-system.evtx`                           |
| PSAttack                                | `.\DeepBlue.ps1 .\evtx\psattack-security.evtx`                            |
| User added to administrator group       | `.\DeepBlue.ps1 .\evtx\new-user-security.evtx`                            |

