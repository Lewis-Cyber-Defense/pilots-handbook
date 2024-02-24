---
title: sysinternals-tools
description: 
published: true
date: 2024-02-24T05:16:39.054Z
tags: 
editor: markdown
dateCreated: 2024-02-24T02:50:29.740Z
---

# TCPView
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

# Process Explorer
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

# Process Monitor
- to investigate if there were any indicators on why the agent was not operating as it should.
-  advanced monitoring: shows real-time file system, registry and process/thread activity 
	- combines features of legacy sysinternals util: filemon, regmon
- **There's going to be a *lot* of events so you'll have to set filters**
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
# Sysmon Overview 
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