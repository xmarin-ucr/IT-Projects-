## Author: Ximena Marín Sánchez

## Table of Contents
- [Introduction](#introduction)
- [Installing VirtualBox and Rocky Linux 9](#installing-virtualbox-and-rocky-linux-9)
- [Custom Partitioning](#custom-partitioning)
- [Connecting via SSH](#connecting-via-ssh)
- [Installing OpenSCAP](#installing-openscap)
- [Running Compliance Tests](#running-openscap-compliance-tests)
- [Installing Firewalld](#installing-firewalld)
- [Fail2Ban Setup](#fail2ban-setup)
- [DNF Automatic Setup](#dnf-automatic-setup)
- [SSH Configuration](#ssh-configuration)
- [Conclusion](#conclusion)

## Introduction
This report aims to document the creation of a base image, commonly referred to as a "golden image" -a virtual machine configured to serve as a template for deploying additional VMs. The focus is on detailing the specific commands and configurations used to build and secure the golden image.

The virtual machine was developed using VirtualBox version 7.2.0 and the Rocky Linux 9 operating system, specifically the Rocky Linux 9.6 Minimal ISO. This version was chosen to exclude a graphical interface and promote learning through command-line interaction.

**For strategic overview of this project and its relevance to cybersecurity roles, see [Project Overview](/Golden_Image/Project_Overview.md)**

## Installing VirtualBox and Rocky Linux 9

1. Visit [VirtualBox Downloads](https://www.virtualbox.org/wiki/Downloads)

2. Download the version appropriate for your host system (e.g., Windows)

3. Follow the installation steps for VirtualBox

4. Visit [Rocky Linux Downloads](https://rockylinux.org/pt-PT/download)

5. Download version 9.6 labeled Minimal ISO

6. Need help with the setup? Follow the step-by-step instructions in the [Installation Guide](/Golden_Image/Guides/Installation_Guide.md)

### Virtual Machine Resource Allocation
The choice of 20 GB disk space, 4096 MB RAM, and 4 CPUs was made to avoid storage limitations and simulate a standard workstation. These values may vary depending on the available resources of the host machine.

### Custom Partitioning
During setup, you can choose between Standard Partition or LVM. In this case, LVM was selected for its scalability advantages.

The partitioning layout used:

| Partition      | Size       | File System  | LVM
| ----------  | :-------------: | ----: | ----:
| /home   | 1 GiB   | ext4 | Sí
| /var/log   | 2 GiB   | ext4 | Sí
| /var/log/audit  | 2 GiB   | ext4 | Sí
| /var/tmp |  1,41 GiB | ext4 | Sí
| / |  7 GiB | ext4 | Sí
| /tmp |  1 GiB | ext4 | Sí
| /var |  2 GiB | ext4 | Sí
| /boot |  1 GiB | ext4 | No
| /boot/efi |  600 MiB | EFI | No
| swap |  7 GiB | SWAP | No

### Why Customize Partitioning?
Custom partitioning gives you greater control over resource allocation and helps prevent disk overflows. Partition sizes should be based on the total disk space assigned to the virtual machine.

Tip: To avoid OpenSCAP compliance errors, make sure to include /var/log/audit and all other recommended partitions. Missing them may lead to issues during security evaluations.

## Using the Rocky Linux 9.6 Minimal ISO
Since this version does not include a graphical interface, only the command line will be visible. At the main menu, simply press ENTER to proceed.

Login credentials will be requested based on the users created during installation—typically root and admin.

## Connecting via SSH
The main reason for enabling SSH is that VirtualBox’s terminal does not allow copy-paste from the host machine, which slows down workflow and increases the chance of typing errors. SSH provides a more efficient alternative, as explained below:

- Open Oracle VirtualBox and go to the main menu

- Right-click the virtual machine and select Settings

- Go to the Network tab

- Set Attached to: Bridged Adapter

- Under Name, select your network controller (e.g., Intel-based)

- Click Accept to apply changes

## How to find your IP Address in Rocky Linux
Once logged into the VM run the following command:
``` 
ip a
```

This command displays the IP addresses assigned to the system. You’ll need this to establish a full-duplex connection between your host machine and the virtual machine.

Look for an IP address that typically starts with 192.

## Open a Terminal on the Host Machine
Now that you have the VM’s IP address, open a terminal on your host system (e.g., WSL, macOS Terminal, or your preferred shell) and run:

```   
ssh admin@[IP address from ip a]
```
This command initiates an SSH connection using the admin user, allowing you to interact with the VM from your host terminal. It enables full-duplex communication and lets you copy-paste commands directly, saving time and reducing errors.

### Installing Nano Editor
Nano is a lightweight text editor commonly used in Unix/Linux systems:

```
sudo dnf install nano
```

## Installing OpenSCAP
```
sudo dnf install scap-security-guide openscap-scanner
```
This command installs openscap-scanner, a tool for security auditing, and scap-security-guide, which provides predefined profiles aligned with common security standards.

These tools are used to strengthen the operating system’s security posture by evaluating and enforcing compliance with industry benchmarks.

## Important Note: Consider Shutting Down the VM After Installation
After installing OpenSCAP, a security policy is applied that may invalidate existing passwords.
This can lead to technical issues such as:

```
Password expired, contact your system administrator
```

To avoid complications, it is recommended to shut down the virtual machine immediately after installation. This preserves the current system state and allows you to restart the process if needed.

Alternatively, consider using VirtualBox snapshots to save your progress at key stages. Snapshots provide a reliable way to roll back to a working configuration without repeating the entire setup. 

```
sudo poweroff
```

### Password Expiration Configuration
If you attempted to shut down the virtual machine after installing OpenSCAP, you may have encountered the following message:

```
You are required to change your password inmediately (password expired)
```

To proceed:

- Enter your current password

- **Choose a new password with at least 14 characters, including uppercase letters, lowercase letters, numbers, and special characters**

- A helpful strategy is to combine multiple words to meet the character requirement

- Recommendation: Since the terminal does not display your input, type slowly to ensure accuracy

### Set a Custom Password Expiration Date

After resetting your password, you can prevent it from expiring prematurely by setting a longer expiration period:

```
sudo chage -M [maximum number of days before expiration] [username]
```

Example: 
```
sudo chage -M 6000 admin

sudo chage -M 6000 root
```
This sets the password to expire in approximately the year 2042.

### Verify Password Expiration Settings
To confirm the new expiration date:

```
chage -l admin
```

```
sudo chage -l root
```

These commands display password-related information for each user, allowing you to verify that the expiration date has been updated successfully.

## Running OpenSCAP Compliance Tests
```
sudo oscap xccdf eval \
  --remediate \
  --profile xccdf_org.ssgproject.content_profile_cis \
  --report compliance-report.html \
  /usr/share/xml/scap/ssg/content/ssg-rl9-ds.xml
```

This command runs a security compliance evaluation based on the CIS profile. It generates a report indicating which rules passed or failed, and it also has the ability to automatically remediate detected issues.

It is used to identify areas for improvement and ensure the system aligns with standard security policies.

*Tip:* You can run this command from your host machine’s terminal for convenience. Running it multiple times may increase the number of rules marked as “Result fixed.”

## Reviewing and Resolving OpenSCAP Failures

See detailed failure and fixes in the [OpenSCAP Remediation Guide](/Golden_Image/Guides/OpenSCAP_Remediation.md)

## Installing Firewalld
To enable Firewalld, the default firewall manager in Rocky Linux:
```
sudo systemctl enable --now firewalld
```
### Firewall Zones
To view the default zone and the expected output may be public:
```
sudo firewall-cmd --get-default-zone
```
To list active zones:
```
sudo firewall-cmd --get-active-zones
```

To inspect services and ports associated with the default zone:

```
sudo firewall-cmd --list-all
```

### Set a Custom Zone
To set the default zone to work, which will be used to trust internal machines:
```
sudo firewall-cmd --set-default-zone work
```

To allow SSH access in the work zone:
```
sudo firewall-cmd --zone=work --add-service=ssh
```

To open TCP port 5000 for communication:
```
sudo firewall-cmd --zone=work --add-port=5000/tcp
```

To make all changes permanent:

```
sudo firewall-cmd --runtime-to-permanent
```

## Prerequisites for Using Fail2Ban
- Using a Rocky Linux 9 system. 
- Firewalld must be running, can check with the following command: 
```
systemctl status firewalld
```

## Fail2Ban Setup
For detailed configuration steps, refer to the guide: [Fail2Ban_Setup](/Golden_Image/Guides/Fail2Ban_Setup.md) 

## DNF Automatic Setup
For detailed configuration steps, refer to the guide: [DNF_Automatic](/Golden_Image/Guides/DNF_Automatic.md)

## SSH configuration 
For detailed configuration steps, refer to the guide: [SSH_Configuration](/Golden_Image/Guides/SSH_Hardening.md)


## Conclusion
This virtual machine was built to meet CIS Level 1 Server benchmarks. Key achievements include:

- Custom partitioning and resource allocation

- SSH hardening and user access controls

- Intrusion prevention with Fail2Ban

- Automated patching via DNF-Automatic

- Firewall configuration with Firewalld

By the end of the setup, the server is equipped with a dynamic firewall, scheduled security updates between 6:00–6:20 AM, and hardened access policies to prevent unauthorized logins and brute-force attacks.