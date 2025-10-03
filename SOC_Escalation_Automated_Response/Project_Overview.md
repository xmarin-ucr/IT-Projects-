# SOC Escalation and Automated Response Playbook

This repository showcases a hands-on cybersecurity lab conducted on World Wide Technology’s training platform, designed to emulate real-world Security Operations Center (SOC) workflows. Throughout the lab, you’ll step into a simulated SOC environment, assuming multiple analyst roles to investigate and respond to a credential-stealing malware incident.

The scenario emphasizes key SOC functions such as alert validation, threat escalation, and automated response, leveraging tools like CrowdStrike Falcon XDR, Security Onion, and Falcon SOAR to detect, analyze, and contain the threat.

## Objectives
- Validate endpoint alerts and escalate threats from Tier 1 to Tier 2.
- Distinguish real threats from false positive.
- Investigate credential theft and lateral movement using Security Onion.
- Trigger automated containment workflows via Falcon SOAR.
- Apply Network Forensics and automation

## Tools Used
- CrowdStrike Falcon: XDR, ITDR, and SOAR (SaaS)
- Security Onion: Suricata, Zeek, and Stenographer
- Sysmon: Custom configuration on Windows VMs
- Atomic Red Team: Adversary simulation payloads
- Impacket: Tools like psexec and smbclient
- Responder + John the Ripper: Credential harvesting and cracking (on Kali Linux) pre-installed in the VM of the platform.
- Falcon Sensor: Pre-installed on all Windows endpoints

## Lab Guide
The lab guide [Lab Guide](/SOC_Escalation_Automated_Response/Lab_Guide.md) is based on the WWT platform but was rewritten using my own steps and observations.
