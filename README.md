# Mr-Robot: 1 — Penetration Testing & VAPT Report

![Cybersecurity](https://img.shields.io/badge/Cybersecurity-Penetration%20Testing-red)
![Platform](https://img.shields.io/badge/Platform-VulnHub-blue)
![Status](https://img.shields.io/badge/Status-Completed-success)

## 📌 Project Overview
This repository contains the documentation and findings of a full penetration test performed against **Mr-Robot: 1**, an intentionally vulnerable Linux virtual machine from VulnHub. 

The objective of this assessment was to simulate a real-world attack, moving from an unauthenticated external attacker to obtaining **full root access** on the target machine, all within an isolated local lab environment.

> **Note:** This project was completed as part of the **IqraSec Academy Phase 2** practical task.

## 🛠️ Lab Environment
* **Attacker Machine:** Kali Linux 2026.2
* **Target Machine:** Mr-Robot: 1 (VulnHub)
* **Hypervisor:** VMware Workstation
* **Network Setup:** Isolated NAT Network (vmnet8)
* **Attacker IP:** `192.168.177.128`
* **Target IP:** `192.168.177.131`

## 🗺️ Attack Methodology (Kill Chain)

The assessment followed a standard penetration testing lifecycle:

1. **Reconnaissance & Enumeration:** 
   * Discovered the target using `arp-scan`.
   * Performed service enumeration using `nmap -sV -sC`.
   * Found Ports `80` (HTTP) and `443` (HTTPS) open.
   * Discovered sensitive files via `robots.txt` (`fsocity.dic` wordlist and `key-1-of-3.txt`).

2. **Vulnerability Identification:**
   * Outdated WordPress (v4.3.1) with XML-RPC enabled.
   * Weak, wordlist-guessable admin credentials.
   * Theme Editor allowing arbitrary PHP file modification.
   * Outdated SUID Nmap binary (v3.81) for privilege escalation.

3. **Exploitation & Foothold:**
   * Used `WPScan` with the exposed wordlist to crack the admin password for the user `elliot`.
   * Logged into the WordPress dashboard.
   * Replaced the `404.php` theme file with a PHP Reverse Shell payload.
   * Triggered the shell to gain a reverse connection as the `daemon` user.

4. **Privilege Escalation:**
   * Enumerated local files and found `key-2-of-3.txt` and a readable raw MD5 password hash.
   * Identified an outdated SUID `nmap` binary.
   * Used the interactive mode (`nmap --interactive`) and the `!sh` shell escape to obtain an **effective root shell** (`euid=0`).

## 🚩 Proof of Compromise
All three hidden keys were successfully captured during the assessment:
* **Key 1:** `073403c8a58a1f80d943455fb30724b9` (Found in `robots.txt`)
* **Key 2:** `822c73956184f694993bede3eb39f959` (Found in `/home/robot/` using `robot` credentials)
* **Key 3:** `04787ddef27c3dee1ee161b21670b4e4` (Found in `/root/` as a root user)

## 📄 Full Report
The complete **Vulnerability Assessment & Penetration Testing (VAPT) Report** contains detailed screenshots, commands used, risk assessments, and remediation advice.

👉 **[Click here to view the Full VAPT Report (PDF)](./VAPT_Phase2_MrRobot_Report.pdf)**
*(Note: Replace the link path above with the actual path where your PDF is stored in this repository).*

## 🛡️ Remediation Highlights
To secure a system against this attack chain, the following actions are recommended:
* Never store sensitive files (wordlists, backups) in the web root or list them in `robots.txt`.
* Keep WordPress core, themes, and plugins updated; disable XML-RPC if not needed.
* Enforce strong, unique passwords and Multi-Factor Authentication (MFA).
* Disable the WordPress built-in file editor (`DISALLOW_FILE_EDIT`).
* Remove unnecessary SUID bits from binaries (like Nmap) and audit them regularly.

---
**Author:** Maryam Batool  
**Date:** November 2026
