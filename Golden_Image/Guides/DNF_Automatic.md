## Installing the dnf-automatic package

Download the tool used to provide software updates.

This command is executed to automate downloads and updates on the Rocky Linux server:

```
sudo dnf install dnf-automatic
```

### Configuring Security Updates

Use the following command to edit and personalized the dnf-automatic timer:

```
sudo systemctl edit dnf-automatic.timer
```

#### DNF-automatic Script

Uncomment the following lines to define a scheduled update configuration. In this case, updates are set to run between 6:00 and 6:20 AM:

```
[Unit]
Description=dnf-automatic timer
# See comment in dnf-makecache.service
ConditionPathExists=!/run/ostree-booted
Wants=network-online.target

[Timer]
OnCalendar=*-*-* 6:00
RandomizedDelaySec=20m
Persistent=true

[Install]
WantedBy=timers.target
``` 

### Enabling the Timer

Run the following command to activate the timer associated with the service, as configured in the previous step:

``` 
sudo systemctl enable --now dnf-automatic.timer
``` 