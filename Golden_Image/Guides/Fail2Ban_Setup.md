## Installing Fail2Ban
Fail2Ban is not available in Rocky Linux’s default repositories, so you must first enable the EPEL repository:
```
sudo dnf install epel-release -y
```

Then install Fail2Ban (IPS):
```
sudo dnf install fail2ban -y
```

### Creating a configuration file to enable SSH protection in Fail2Ban

Create a file named jail.local inside the /etc/fail2ban directory:

```
sudo nano /etc/fail2ban/jail.local
```

This configuration enhances security policies by preventing brute-force attacks, especially when there are a high number of login attempts within a short time frame.

Inside the text editor, add the following content:

* The parameter enabled = true activates SSH protection

* The parameter logpath specifies the location of the log files

```
[DEFAULT]
# Ban hosts for fifteen minutes:
bantime = 900

# Maxium of tries in ten minutes
findtime = 10m
maxretry = 3

[sshd]
enabled = true
port = ssh
logpath = %(sshd_log)s
backend = %(sshd_backend)s
```

## Verifying Fail2Ban is running

Use the following command to check whether Fail2Ban is running and properly configured. For example, the output may show Tasks: with the value defined by maxretry.

```
sudo systemctl status fail2ban
```

### Start automatically

To ensure Fail2Ban starts on system boot, run:

```
sudo systemctl enable fail2ban
```

