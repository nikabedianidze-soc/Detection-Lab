# 🔎 Detection Lab — SOC Investigation

![Kali Linux](https://img.shields.io/badge/Kali%20Linux-557C94?&style=for-the-badge&logo=kalilinux&logoColor=white)
![Windows 11](https://img.shields.io/badge/Windows%2011-0078D4?&style=for-the-badge&logo=windows&logoColor=white)
![Splunk](https://img.shields.io/badge/Splunk-000000?&style=for-the-badge&logo=splunk&logoColor=white)
![Sysmon](https://img.shields.io/badge/Sysmon-0078D4?&style=for-the-badge&logo=microsoft&logoColor=white)
![Nmap](https://img.shields.io/badge/Nmap-4682B4?&style=for-the-badge&logoColor=white)
![Metasploit](https://img.shields.io/badge/Metasploit-2596CD?&style=for-the-badge&logo=metasploit&logoColor=white)

## 📌 Overview

This project is a hands-on **SOC detection and investigation lab** designed to simulate an end-to-end attack
against a Windows 11 endpoint and investigate the resulting activity using **Splunk and Sysmon**.

The lab was built to understand the relationship between attacker activity and the telemetry generated on an endpoint.

The attack simulation followed this general workflow:

```text
Reconnaissance
      ↓
Port Scanning
      ↓
Payload Generation
      ↓
Payload Delivery
      ↓
Payload Execution
      ↓
Reverse Shell / C2
      ↓
Endpoint Telemetry
      ↓
Splunk Detection
      ↓
Investigation
      ↓
Attack Timeline Reconstruction

⚠️ Disclaimer: This project was performed in an isolated home lab using virtual machines that I own and control.
The payload and attack techniques were used strictly for educational, defensive security research.
No third-party systems were targeted.

🎯 Objectives

The main objective was to build a small SOC environment and simulate an attack against a Windows endpoint while collecting and
investigating security telemetry.

This project focused on:

Building an isolated cybersecurity home lab
Configuring network communication between virtual machines
Performing network reconnaissance
Identifying open ports and services
Generating a controlled malicious payload
Simulating payload delivery
Simulating a reverse shell / C2 connection
Collecting Windows endpoint telemetry
Using Sysmon for detailed event monitoring
Using Splunk as a SIEM
Investigating suspicious activity
Reconstructing the attack timeline
Identifying indicators of compromise
Mapping observed activity to MITRE ATT&CK

🏗️ Lab Architecture

The environment consisted of two virtual machines connected through an isolated LAN.

                         ISOLATED LAB NETWORK
                                  │
                 ┌────────────────┴────────────────┐
                 │                                 │
                 │                                 │
          ┌──────▼──────┐                   ┌──────▼──────┐
          │    Kali     │                   │  Windows 11 │
          │   Linux     │                   │   Endpoint  │
          │             │                   │             │
          │ Attack VM   │                   │ Victim VM   │
          │             │                   │             │
          │ Nmap        │                   │ Splunk      │
          │ MSFvenom    │                   │ Sysmon      │
          │ Metasploit  │                   │             │
          │ Python HTTP │                   │             │
          └─────────────┘                   └─────────────┘

💻 Virtual Machines

Machine	Role	Main Purpose
Kali Linux	Attack System	Reconnaissance, payload generation, delivery and C2 simulation
Windows 11	Victim / Endpoint	Payload execution and security telemetry collection

🛠️ Tools & Technologies

Operating Systems
Kali Linux
Windows 11
SIEM & Endpoint Monitoring
Splunk
Sysmon
Windows Event Logs
Network Security
Nmap
Wireshark
TCP/IP networking
Attack Simulation
Metasploit Framework
MSFvenom
Meterpreter
Python HTTP Server
Investigation
Splunk Search Processing Language (SPL)
Sysmon Event IDs
Windows process telemetry
Network connection telemetry
MITRE ATT&CK

⚙️ Lab Setup
1. Virtual Machine Network Configuration

I created two virtual machines:

Kali Linux
Windows 11

Both systems were configured on the same isolated LAN network to allow controlled communication between the attack and victim machines.

Kali Linux was used as the attack simulation system, while Windows 11 acted as the monitored endpoint.

2. Windows Endpoint Configuration

Splunk was installed on the Windows 11 machine to collect and analyze security telemetry.

Sysmon was also installed and configured to provide detailed endpoint visibility.

Sysmon provided telemetry related to:

Process creation
Process command lines
Parent-child process relationships
Network connections
File activity
Registry activity
User activity

For this controlled lab environment, Windows Defender and Windows Firewall were temporarily disabled to allow the simulated payload
to execute and communicate with the Kali Linux machine.

⚠️ This configuration was used only inside the isolated lab environment and should never be replicated on production systems.

🔴 Attack Simulation
3. Network Reconnaissance

The first stage of the simulation was reconnaissance.

From Kali Linux, I scanned the Windows 11 endpoint to identify reachable services, open ports and the potential attack surface.

Nmap was used to perform the scan.

Kali Linux
     │
     │ Nmap
     ▼
Windows 11
     │
     ├── Open Ports
     ├── Services
     └── Attack Surface
Investigation Goal

The objective of this stage was to understand how an attacker could identify exposed services before attempting further activity.

4. Payload Generation

For the controlled attack simulation, I used MSFvenom to generate a Windows executable containing a Meterpreter reverse-shell payload.

The payload was configured to communicate back to the Kali Linux machine using a predefined listener address and port.

The generated executable was given the filename:

malware.pdf.exe

This was intentionally used to demonstrate a simple masquerading / deceptive filename technique where an executable attempts to appear similar to a document.

5. Metasploit Multi/Handler

A Metasploit multi/handler listener was configured on Kali Linux.

The listener was configured to match the connection parameters used by the generated payload.

The simulated communication flow was:

Windows 11
     │
     │ Reverse Connection
     ▼
Kali Linux
     │
     ▼
Metasploit Multi/Handler
     │
     ▼
Meterpreter Session
6. Payload Delivery

To simulate a basic attacker-controlled file delivery mechanism, Python's built-in HTTP server was used on Kali Linux.

The malicious executable was hosted on the Kali machine and made available to the Windows endpoint over HTTP.

┌───────────────┐
│   Kali Linux  │
│               │
│ HTTP Server   │
│       │       │
│       │ HTTP  │
└───────┬───────┘
        │
        ▼
┌───────────────┐
│   Windows 11  │
│               │
│ Downloads     │
│ malware.pdf.exe│
└───────────────┘

This simulated a simple malicious file delivery scenario.

7. Payload Execution

The executable was downloaded to the Windows endpoint and executed manually.

The Meterpreter payload then attempted to establish a reverse connection back to the Kali Linux listener.

The execution generated endpoint telemetry that could be investigated through Sysmon and Splunk.

🔍 Detection & Investigation
8. Splunk Investigation

After the payload was executed, I investigated the resulting activity in Splunk.

The goal was not only to confirm that the payload executed, but to understand what happened on the endpoint before, during and after execution.

The investigation focused on:

Process creation
Command-line activity
Parent-child process relationships
Network connections
Source IP addresses
Destination IP addresses
Destination ports
Suspicious executable activity
User activity
Timeline reconstruction
Potential indicators of compromise

🧩 SOC Investigation Workflow

The investigation followed a typical SOC-style workflow:

                    Suspicious Activity
                            │
                            ▼
                      Identify Host
                            │
                            ▼
                      Identify Process
                            │
                            ▼
                    Analyze Command Line
                            │
                            ▼
                 Identify Network Connection
                            │
                            ▼
                  Correlate Sysmon Events
                            │
                            ▼
                    Build Attack Timeline
                            │
                            ▼
                  Identify IOCs / Artifacts
                            │
                            ▼
                    Determine Root Cause
📊 Telemetry Analysis

Sysmon provided detailed endpoint telemetry that allowed the activity to be investigated from the Windows host perspective.


🗺️ MITRE ATT&CK Mapping

The simulated activity can be mapped to several MITRE ATT&CK techniques.

Tactic	Technique	ID	Lab Activity
Reconnaissance	Network Service Scanning	T1046	Nmap scanning
Execution	User Execution	T1204	Executing the downloaded executable
Command & Control	Ingress Tool Transfer	T1105	Transferring the executable to the endpoint
Command & Control	Application Layer Protocol: Web Protocols	T1071.001	HTTP used for file delivery
Command & Control	Non-Application Layer Protocol	T1095	Reverse-shell communication

Technique mapping describes the simulated activity in this lab. Exact ATT&CK mapping can vary depending on the implementation and telemetry observed.

🚨 Indicators of Compromise

During the investigation, the following types of artifacts were examined:

File Name
    └── malware.pdf.exe

Source IP
    └── Kali Linux IP

Destination IP
    └── Windows 11 IP

Destination Port
    └── Configured listener port

Process
    └── Executed malicious executable

Network Connection
    └── Reverse connection to Kali Linux

Actual IP addresses, ports, hashes and other environment-specific values can be documented here when publishing screenshots and investigation results.


💡 Skills Demonstrated
SOC Operations
Security Monitoring
SIEM
Splunk
Sysmon
Windows Security
Log Analysis
Network Analysis
Incident Investigation
Threat Detection
Network Reconnaissance
Nmap
Metasploit
Wireshark
MITRE ATT&CK
Attack Timeline Reconstruction
Endpoint Detection

🚀 Future Improvements

I plan to expand this detection lab by implementing additional defensive security capabilities.

📚 What I Learned

The most important lesson from this project was understanding the relationship between attacker actions and defensive telemetry.

Instead of only focusing on how an attack is performed, I focused on understanding what evidence the attack leaves behind.


🏁 Conclusion

This project demonstrates an end-to-end SOC detection and investigation workflow in a controlled environment.

I built an isolated network containing Kali Linux and Windows 11, simulated an attack against the Windows endpoint, generated endpoint telemetry using Sysmon, collected the telemetry with Splunk, and investigated the resulting activity.

The project covered the complete lifecycle:

Reconnaissance
      ↓
Payload Generation
      ↓
Payload Delivery
      ↓
Execution
      ↓
Command & Control
      ↓
Telemetry Collection
      ↓
Detection
      ↓
Investigation
      ↓
Attack Reconstruction

The primary focus of the project was not simply performing the attack, but understanding how the attack appeared from a SOC analyst's perspective through logs and endpoint telemetry.

👤 Author

Nika Bedianidze

Cybersecurity | SOC Analyst | Blue Team
