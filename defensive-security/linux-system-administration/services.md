---
title: Services
description: 
published: true
date: 2024-02-23T15:05:26.231Z
tags: 
editor: markdown
dateCreated: 2024-02-22T06:13:47.556Z
---

# Enable/Disable Services
Services can be enabled or disabled with `sudo systemctl enable <service> --now` and `sudo systemctl disable <service> --now` respectively.

Note that services can still be ran after being disabled. To truly disable them, run `systemctl mask <service>` afterwards.

To quickly restart a service, run `sudo systemctl restart <service>`

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
Full setup guide for BIND DNS [here](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/9/html/managing_networking_infrastructure_services/assembly_setting-up-and-configuring-a-bind-dns-server_networking-infrastructure-services)

Setup guides for AD DNS [here](https://docs.rackspace.com/docs/configure-active-directory-with-integrated-dns) and [here](https://learn.microsoft.com/en-us/windows-server/networking/dns/quickstart-install-configure-dns-server?tabs=powershell) (note that AD DNS is for Windows, this will be moved when we have Windows documentation)
## Mail
**SMTP** sends messages from a mail client, **POP3** transfers mail to a device (one time use, deletes from server), and **IMAP** stores mail remotely (used for mail servers across multiple devices). If that's confusing, think of it this way:
- SMTP moves mail from one server to another
- POP3 lets you download the mail, removing it from the server
- IMAP lets you read the mail from the server itself

SMTP is used with either IMAP or POP3 to send and receive email.

[Dovecot](https://www.dovecot.org/) is frequently used as a Mail Delivery Agent (MTA), to run POP3 and IMAP.

[Postfix](http://www.postfix.org/) is frequently used as a Mail Transfer Agent (MTA), to run SMTP.
## Telnet
Telnet setup guide [here](https://linuxtiwary.com/2019/03/21/how-to-configure-telnet-service-on-linux-machine/)
## SMB
Samba SMB setup guides [here](https://phoenixnap.com/kb/ubuntu-samba) and [here](https://ubuntu.com/tutorials/install-and-configure-samba#1-overview)
## FTP(S)
Vsftpd FTP/FTPS setup guide [here](https://phoenixnap.com/kb/install-ftp-server-on-ubuntu-vsftpd)
# Ports based on service
| Service | Port |
| ---- | ---- |
| FTP | TCP: 20 for data transfer<br>TCP: 21 for control |
| FTPS | TCP: 989 for data transfer<br>TCP: 990 for control |
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