# General (multi-OS)
## Automation
- [Ansible](https://www.ansible.com/)
## Traffic analysis
- [Wireshark](https://www.wireshark.org/)
## Antivirus
- [ClamAV](https://www.clamav.net/)
## Scanners
- [Openvas](https://github.com/greenbone/openvas-scanner)
- [Nessus](https://www.tenable.com/products/nessus)
- [NMAP](https://nmap.org/)
## Log aggregation
- [Splunk](https://www.splunk.com/)
- [Netdata](https://github.com/netdata/netdata)
- [ELK](https://www.elastic.co/elastic-stack)
## Firewalls
- [Palo Alto](https://www.paloaltonetworks.com/)
# Linux
## Logging
- [Kunai](https://github.com/kunai-project/kunai)
- [SysmonForLinux](https://github.com/Sysinternals/SysmonForLinux)
- [Auditd](https://www.redhat.com/sysadmin/configure-linux-auditing-auditd)
	- This one is the most low-level and works well on most systems
	- [Config](https://github.com/Neo23x0/auditd/tree/master)
## Threat Hunting/EDR
- [Chopchopgo](https://github.com/M00NLIG7/ChopChopGo)
- [Louis](https://github.com/sourque/louis)
- [AIDE](https://github.com/aide/aide)
- [bluebpf](https://github.com/mttaggart/bluebpf)
## IDS/IPS
- [Snort](https://github.com/snort3/snort3)
- [Suricata](https://github.com/OISF/suricata)
- [Zeek](https://github.com/zeek/zeek)
## Firewalls
- [IPTables](https://linux.die.net/man/8/iptables)
## Traffic Protection
- [Fail2Ban](https://github.com/fail2ban/fail2ban)
## Process analysis
- [Pspy](https://github.com/DominicBreuker/pspy)
- [`htop`](https://github.com/htop-dev/htop)
- [`ps`](https://man7.org/linux/man-pages/man1/ps.1.html)
## Network analysis
- [`ss`/`netstat`](https://man7.org/linux/man-pages/man8/ss.8.html) (ss is better)
## Users
- [`w`](https://man7.org/linux/man-pages/man1/w.1.html)
- [`who`](https://man7.org/linux/man-pages/man1/who.1.html)
## Scanners
- [LinPEAS](https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS)
- [RKHunter](https://rkhunter.sourceforge.net/)
## Other
- [Linux Hardening Checklist](https://github.com/trimstray/linux-hardening-checklist)
- [Linux Backdoor Guide](https://github.com/gquere/linux_backdooring)
# Windows
## Logging
- [Sysinternals](https://learn.microsoft.com/en-us/sysinternals/downloads/sysinternals-suite) - Sysmon
	- [Config](https://raw.githubusercontent.com/olafhartong/sysmon-modular/master/sysmonconfig-with-filedelete.xml)
	- [SysmonView](https://github.com/nshalabi/SysmonTools) (helps look through sysmon logs)
- Windows Security Event Logs
## Process analysis
- [Sysinternals](https://learn.microsoft.com/en-us/sysinternals/downloads/sysinternals-suite) - Procmon
- [Sysinternals](https://learn.microsoft.com/en-us/sysinternals/downloads/sysinternals-suite) - Process Explorer
- Windows Task Manager
## Network analysis
- [Sysinternals](https://learn.microsoft.com/en-us/sysinternals/downloads/sysinternals-suite) - TCPView
## Firewall
- Windows Firewall
## Antivirus
- Windows Defender
- [MalwareBytes](https://www.malwarebytes.com/)
## IDS/IPS
- (insert tools)
## Threat Hunting/EDR
- [DeepBlueCLI](https://github.com/sans-blue-team/DeepBlueCLI)
- [PersistenceSniper](https://github.com/last-byte/PersistenceSniper)
- [Chainsaw](https://github.com/WithSecureLabs/chainsaw)
## Scanners
- [WinPEAS](https://github.com/carlospolop/PEASS-ng/tree/master/winPEAS)
## Other
- [SecureAD](https://github.com/LoicVeirman/SecureAD)
- [Powershell Incident Cheat Sheet](https://gist.github.com/RomelSan/9ebef17aa9aa061d6b32e2e250181942)
- [Awesome Windows Domain Hardening](https://github.com/PaulSec/awesome-windows-domain-hardening)