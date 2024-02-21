# General Hardening
There's a few mindsets to use when hardening. Easiest is the *prevent all unauthorized access* mindset, which is great to quickly prevent initial access and to limit attacker capabilities. That comes down to just a few things:
1. Change credentials and lock/remove accounts ([guide](os-info.md))
2. Disable services ([guide](services.md))
3. Ensure nothing is reaching out that shouldn't be (more info [here](incident-response))
	1. Check crontabs for network connections
	2. Check important system files for backdoors ([guide](os-info))
4. Enable firewall (explained below)
5. Secure services (explained below)

The next mindset is realizing that an attacker **will** get into the system as long as a service is up, so you have to prevent privilege escalation. While this list is shorter, it's also a little more complicated:
1. Check file and executable permissions, including SUID/SGID ([guide](os-info.md))
2. Check important system files, like `/etc/passwd`, `/etc/sudoers`, etc, for misconfigurations ([guide](os-info.md))
3. Check for privilege escalation vectors, like `env`, plaintext passwords, or suspicious files ([guide](os-info.md))
4. Backup important files/directories

Note that having multiple eyes and rotation on machines may help find new vulnerabilities, but sticking to a machine may build familiarity.

Remember to set up logging after any hardening! ([guide](logging.md))
# Service Hardening
## SSH
SSH configuration isn't *too* difficult. [Here](https://help.ubuntu.com/community/SSH/OpenSSH/Configuring) is a guide to setting up the config file (`/etc/ssh/sshd_config`)

And here is a high-level overview:
1. Set `PermitRootLogin` to `no`
2. Create a whitelist with `AllowUsers <user1> <user2>`
3. Create a blacklist with `DenyUsers <user1> <user2>`
4. Set `PermitEmptyPasswords` to `no`
5. Set `PasswordAuthentication` to `no`
	1. This requires using an ssh key, meaning devices must be pre-approved and password login is disabled
6. Set `AllowTcpForwarding` and `X11Forwarding` to `no`
7. Set `LogLevel` to `VERBOSE`
8. Display banners with `Banner /etc/issue`
9. Restart SSH: `systemctl restart ssh`

## Apache2
Apache configuration is located in `/etc/apache2/`

A hardening guide can be found [here](https://hostadvice.com/how-to/web-hosting/ubuntu/how-to-harden-your-apache-web-server-on-ubuntu-18-04/) but note that apache itself isn't usually vulnerable, it's whatever sites the apache service is hosting.

Ports can be set in `/etc/apache2/ports.conf`, and IPv6 can be disabled in this file by setting `Listen 80` and `Listen 443` to `Listen 0.0.0.0:80` and `Listen 0.0.0.0:443`

Installing `libapache2-mod-security2` and `libapache2-mod-evasive` and restarting apache2 can protect against DDOS and brute forcing.

Web servers are usually located in `/var/www/html/<website>` which can be accessed with `http://localhost:80/<website>`
## MySQL
Many websites use a MySQL database. Access the database with the `sudo mysql` command.

Make sure to change root password for the database:
```sql
UPDATE mysql.user SET Password=PASSWORD('<password>') WHERE User='root';
FLUSH PRIVILEGES;
```
If you get `ERROR 1054` use the following:
```sql
ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass';
FLUSH PRIVILEGES;
```

A guide to changing prestashop passwords can be found [here](https://www.inmotionhosting.com/support/edu/prestashop/prestashop-admin-password-database/). If we don't have cpanel or phpmyadmin access, just use SQL:
```sql
UPDATE `ps_employee` SET `passwd` = MD5('<_COOKIE_><password>') WHERE `ps_employee`.`id_employee` = 1;
```

Try to change passwords in the databases based on the services running. Figuring out how to do this will require research based on services.

**Note:** phpmyadmin is a great MySQL management software, it can be installed with the `phpmyadmin` package. A setup guide can be found [here](https://help.ubuntu.com/community/phpMyAdmin)

## SMTP

# IPTables/Firewall

# Fail2Ban IP Lockouts


# Backups

