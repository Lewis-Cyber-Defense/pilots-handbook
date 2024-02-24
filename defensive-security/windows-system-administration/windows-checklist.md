- This is an impromptu checklist of sorts. Mostly what I plan to do during MWCCDC competition day. No set order, but more so the priority of tasks. Will fix this up to be more concrete later

# Setup and Harden Phase: 
- Change the password (obviously)
	- Windows makes this easy
- Set up logging
	- [Download and set up sysinternals and sysmon](windows-system-administration/sysinternals-setup-and-basics.md) on the machines
- [Harden](windows-system-administration/hardening.md) the systems
	- Run hardening scripts on Windows and Windows Servers machines
	- Do some manual Active Directory Hardening
- Turn on Windows Defender or/then download Malwarebytes 
- Windows Firewall is pretty garbage but the main thing is turn it on lmfao (more info below)
- Download [DeepBlueCLI](windows-system-administration/deep-blue-cli.md) for threat hunting
# Incident Response Phase: 
- Run [incident response scripts](windows-system-administration/hardening.md)
- Scan machine with [winPEAS](windows-system-administration/winpeas.md)
- Use the downloaded [sysinternals tools](windows-system-administration/sysinternals-tools.md) to monitor system
	- tcpview (network analysis)
	- procexp (process analysis)
	- procmon (process analysis))
	- sysmon (logging)
		- This uses Event Viewer
	- Other tools: 
		- autoruns (I found this useful for the first qualifier)
		- osquery
- Use [DeepBlueCLI](windows-system-administration/deep-blue-cli.md) for threat hunting

# Firewalls
- Will create its own section after doing more research on this post-competition
- Edit the firewall through group policy (for servers)
	- Group Policy Management > GPO > Rt-click and edit
		- > Computer Configuration > Policies > Windows Settings > Security Settings > Windows Firewall with Advanced Security.
		- Might have to link GPO to specific OU (organizational unit) or complete an update
- If/when needed, you can set up basic rules like blocking IP addresses through Windows Firewall
- Viewing Firewall Logs
	- Event Viewer > Windows Logs > Security > "Filter Current Log" > Event Sources (Windows Firewall) > Keywords (Audit Success and Failure) > OK
# Things to Generally Keep in Mind 
- Disable services by:
	- Open 'services' > stop service > disable service
- There will be a lot of logs
- **Log files reside in** (windows event viewer): 
```
C:\Windows\System32\winevt\Logs
```
 