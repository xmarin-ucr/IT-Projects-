# SOC Escalation and Automated Response Playbook

This repository documents a hands-on cybersecurity lab completed on World Wide Technology’s training platform. The lab simulates real-world SOC operations, including alert validation, threat escalation, and automated response using Falcon XDR and SOAR.

## Objectives
- Validate endpoint alerts and escalate threats from Tier 1 to Tier 2.
- Distinguish real threats from false positive.
- Investigate credential theft and lateral movement using Security Onion.
- Trigger automated containment workflows via Falcon SOAR.
- Apply Network Forensics and automation

## Tools Used
- CrowdStrike Falcon XDR, ITDR, and SOAR (SaaS)
- Security Onion with Suricata, Zeek, and Stenographer
- Sysmon with custom config (on Windows VMs)
- Atomic Red Team payloads
- Impacket (psexec, smbclient)
- Responder + John the Ripper (on Kali)
- Falcon Sensor – pre-installed on all Windows endpoints

## Lab Guide
The lab guide [Lab Guide](/SOC_Escalation_Automated_Response/Lab_Guide.md) is based on the WWT platform but was rewritten using my own steps and observations.
