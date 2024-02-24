# Sysinternals Download and Setup
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