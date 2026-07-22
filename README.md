# Containerized Deception Defense & Forensic Log Analysis
> **A Low-Interaction Dionaea Honeypot Lab Project**

## 📌 Executive Summary
In emerging digital economies, organizations face constant, automated reconnaissance from botnets and malicious actors scanning for unpatched services. High-tier commercial defensive solutions are often cost-prohibitive. 

This project demonstrates how a **low-interaction honeypot** can be deployed using lightweight, open-source containerization (Docker) to safely capture, monitor, and analyze hostile network activity without risking production data or real infrastructure.

---

## 🛠️ Tech Stack & Tools
* **Virtualization & Containers:** Linux / Docker
* **Deception Engine:** Dionaea Honeypot
* **Reconnaissance Tools:** Nmap
* **Traffic Analysis & Forensics:** Wireshark
* **Operating System:** Linux / Ubuntu / WSL

---

## 🏗️ Architecture & Setup
The lab setup consists of three primary components:
1. **The Target Node:** A Dockerized Dionaea honeypot container mimicking high-value vulnerable ports.
2. **The Attacker Node:** A separate workstation executing targeted reconnaissance.
3. **The Network Tap:** An active Wireshark packet capture interface sniffing transport-layer traffic.

### Emulated Services:
* **FTP:** Port 21
* **HTTP:** Port 80
* **SMB:** Port 445
* **MySQL:** Port 3306

---

## 🔬 Practical Findings & Key Analysis

### 1. Offensive Reconnaissance (Nmap)
An aggressive service detection scan was launched against the honeypot IP. Nmap successfully detected open ports; however, the honeypot planted a custom banner response on Port 445:
`Dionaea honeypot smbd`
This verified that the trap effectively tricked automated scanners into classifying the host as a vulnerable SMB server.

### 2. Network-Layer Inspection (Wireshark)
Using Wireshark, the raw TCP transport mechanics were captured:
* Captured rapid stealth `[SYN]` request bursts followed by immediate connection tears (`[RST]`) to map active ports without opening full sessions.
* Analyzed deep packet parameters (`Seq`, `Win`, `Mss` fields) that scanners utilize for remote OS fingerprinting.

### 3. Container Forensics & Constraints (Docker Logs)
Inspection of internal container logs yielded two major security insights:
* **Dynamic Emulation:** When Port 3306 was probed, the logs confirmed dynamic memory creation:  
  `mysql /dionaea/mysql/mysql.py:83: DATABASE opening information_schema`
* **Low-Interaction Limitations:** Aggressive scanning overwhelmed the emulated HTTP service script, throwing a Python `ReferenceError` traceback in the logs. This highlighted a key academic limitation: simple script-based low-interaction honeypots can be fingerprinted or crashed when subjected to heavy automated scans.

---

## 🎓 Key Learnings
* Setting up containerized deception technologies as a cost-effective threat intelligence solution.
* Analyzing real-world packet mechanics and stealth scanning behaviors.
* Evaluating the operational trade-offs and limits of low-interaction security systems.
