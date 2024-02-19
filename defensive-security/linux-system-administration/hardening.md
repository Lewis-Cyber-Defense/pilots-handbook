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
[guide](https://www.ssh.com/academy/ssh/sshd_config)


## Apache2


# IPTables/Firewall

a
# Fail2Ban IP Lockouts
