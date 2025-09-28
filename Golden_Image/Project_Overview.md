## Golden Image Project by Ximena Marín

## Overview
This project was developed as part of the Infrastructure of IT course at University of Costa Rica using Rocky Linux 9.6 virtual machine designed to meet CIS Level 1 Server benchmarks.

It simulates a production-ready environment with SSH protection, and intrusion prevention, ideal for roles in cybersecurity, infrastructure, and risk assessment.

The configuration was built using the Minimal ISO to emphasize command-line proficiency and system-level control. All steps were executed manually to reinforce understanding of Linux internals, security policies, and compliance tooling.

## Language Availability

This project is documented in two languages:

* [English Version](/Golden_Image/Report_Golden_Image.md)

* [Spanish Version](/Golden_Image/Reporte_Golden_Image.md)

## Why this project matters?
It demonstrates:

- Proactive threat mitigation using Fail2Ban and SSH hardening

- Automated vulnerability patching with DNF-Automatic

- Firewall segmentation and zone trust modeling via Firewalld

- Security policy auditing and remediation using OpenSCAP

- Documentation clarity and reproducibility for collaborative environments

## The tools and guides referenced throughout this report include:

- CIS Rocky Linux 9 Benchmark

- [Automated security hardening with OpenSCAP](https://tech-couch.com/post/automated-security-hardening-on-rockylinux-with-openscap)

- [Firewalld](https://docs.rockylinux.org/guides/security/firewalld-beginners/)

- [Fail2ban](https://www.digitalocean.com/community/tutorials/how-to-protect-ssh-with-fail2ban-on-rocky-linux-9)

- [DNF Automatic](https://docs.rockylinux.org/guides/security/dnf_automatic/)

## Purpose of the tools
| Tool      | Purpose   |    
| ----------  | :-------------: |
Firewalld	| Dynamic firewall management |
Fail2Ban	| SSH brute-force protection |
OpenSCAP	| CIS compliance auditing and remediation |
DNF-Automatic	| Scheduled security updates |
SSH	| Key-based authentication and access control |
VirtualBox | VM provisioning and simulation | 

## Skills Demonstrated

1. Linux system administration

2. Cybersecurity fundamentals

3. Compliance benchmarking (CIS)

4. Automation and scripting

5. Technical documentation

6. Fast learning and troubleshooting

7. Perseverance and problem-solving

## Repository Structure

```
Golden_Image/
├── Reporte_Golden_Image.md
├── Report_Golden_Image.md
├── Project_Overview.md
├── Guides/
|  ├── Installation_Guide.md
|  ├── Openscap_Remediation.md
|  ├── Fail2ban_Setup.md
|  ├── DNF_Automatic.md
|  ├── SSH_Hardening.md
```

