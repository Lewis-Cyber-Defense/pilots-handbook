
## Sysmon Download

### Download from URL

You can find the Sysmon binary from the [Microsoft Sysinternals](https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon) website. 

(I haven't tried these commands to see if they work):
```none
Download-SysInternalsTools C:\Sysinternals
```

```none
OR
```

```none
Install-Module -Name SysInternals
```

**After download, you can launch tools from command line just by typing the program name in (elevated) PowerShell or Command Prompt** (Need to edit environment variables):
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

## Sysmon Config File

### SwiftOnSecurity

- SwiftOnSecurity is a very popular and well known Sysmon configuration file:

- https://github.com/SwiftOnSecurity/sysmon-config

- Generally really good, although it can be improved.

**Once downloaded, run Command Prompt as Administrator:**
```
Sysmon.exe -accepteula -i ..\PathToConfigFile\confFile.xml
```

### Neo23x0's Modified Fork of SwiftOnSecurity

**NOTE:** This one might be genuinely better than SwiftOnSecurity, as it is decently popular and is getting frequent Github pushes, but it's untested by us.

- Fork of the original SwiftOnSecurity File. 

- Better detection against CobaltStrike, PrinterNightmare and HiveNightmare

- Use the `sysmonconfig-export-block.xml` file in the repo

- [The Repo](https://github.com/Neo23x0/sysmon-config/blob/master/sysmonconfig-export-block.xml)
## Manual Modifications
By default, `EventID: 7` is disabled by default, because it is quite noisy, which is especially bad if you plan on using Splunk, and hit that data limit.

- `EventID: 7` corresponds to module load events, and is one of the only few ways in which you can detect DLL Hijacking. 

- To enable it, go down to `eventID: 7`, and replace the "include" with "exclude"
