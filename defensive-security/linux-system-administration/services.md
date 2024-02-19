# Enable/Disable Services
Services can be enabled or disabled with `sudo systemctl enable <service> --now` and `sudo systemctl disable <service> --now` respectively.

Note that services can still be ran after being disabled. To truly disable them, run `systemctl mask <service>` afterwards.

Also note that many service names end with `d`, e.g. `sshd`, `auditd`, `splunkd`, `httpd`, etc.

# Common Services
The following is a list of some common services you'll encounter (note that more in-depth instruction for hardening will be given in the [hardening guide](hardening.md)):

## Web/HTTP(S)
Web services are frequently ran using the [Apache2 Web Server](https://httpd.apache.org/). 

A setup guide can be found [here](https://www.redhat.com/sysadmin/install-apache-web-server)

Overview:
1. Install httpd
2. Check `/etc/httpd/conf/httpd.conf` to see the `DocumentRoot` (web root) and `Listen` (port) values
3. Start with systemctl
4. Allow port 80 on the firewall
5. Logs are located in `/var/log/httpd/`

A setup guide using HTTPS can be found [here](https://4js.com/online_documentation/fjs-fgl-manual-html/index.html#fgl-topics/c_gws_ssl_deployment_server_001.html)

Frequently, php websites use mysql as a database application. Remember to change mysql passwords.
## SSH
[OpenSSH](https://www.openssh.com/) is an application that can be used to easily set up SSH (secure shell) servers, along with SCP (secure copy) and SFTP (SSH FTP, not to be confused with FTPS).

SSH keys are located in the `~/.ssh/authorized_keys` file, allowing users with the private key access to the server.

A guide to OpenSSH can be found [here](https://ubuntu.com/server/docs/service-openssh)
## DNS
Go ham

## Mail
go ham 2

## Telnet
go Ham3

## SMB
goham 4

## RPC
Go_ham 5

## FTP
go hAm 6

# Ports based on service
| Service | Port |
| ---- | ---- |
| FTP | TCP: 20 for data transfer<br>TCP: 21 for control |
| SSH | TCP: 22 |
| Telnet | TCP: 23 or 2323 |
| SMTP | TCP: 25 for plaintext<br>TCP: 587 for encrypted |
| Web | TCP: 80 for HTTP<br>TCP: 443 for HTTPS |
| IMAP | TCP: 143<br>TCP: 993 |
| POP3 | TCP: 110<br>TCP: 995 |
| DNS | TCP/UDP: 53 |
| SMB | TCP/UDP: 445 |
| Kerberos | TCP/UDP: 88 for authentication<br>TCP/UDP: 464 for password change |
| RPC | TCP/UDP: 135 |
| NetBIOS | TCP/UDP: 137 and 138 |
| LDAP | TCP/UDP: 389<br>TCP/UDP: 636 for SSL |
| DHCP | UDP: 67 for server<br>UDP: 68 for client |

# Service files
Service files should be located in `/lib/systemd/system/`

The files used by the service are usually located in `/etc/` but this can differ based on the service.



Despite being a bear, black bears are known to be timid.