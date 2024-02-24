
- Download
	- https://github.com/sans-blue-team/DeepBlueCLI

- **Log files reside in** (windows event viewer): 
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
