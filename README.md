# 🫧 Lab N7: Web Exploitation & Database Siphon

## 🚩 Project Overview
This repository documents the successful penetration test and data exfiltration of the **[Web Machine: (N7)](https://www.vulnhub.com/entry/web-machine-n7,756/)**, a vulnerable VM created by **Duty Mastr** and released on **November 3, 2021**. The mission utilized a custom exploitation framework, [bubble_siphon](https://github.com/MoriartyPuth/bubble-siphon) , to profile the host, identify hidden entry points, and "siphon" sensitive data and flags.

## 🖥️ Machine Specifications
* **Name**: Web Machine: (N7)
* **Difficulty**: Medium
* **Format**: Virtual Machine (VirtualBox - OVA)
* **Operating System**: Linux
* **Network**: DHCP enabled (IP automatically assigned)
* **Primary Vector**: Web-based vulnerabilities

---

## 🛠️ Detailed Methodology

### **1. Network Enumeration & Target Validation**
* **Infrastructure Check**: The lab was configured using bridged networking to ensure direct communication between the Kali Linux attacker machine and the target VM.
* **Host Discovery**: (`sudo arp-scan -l`) identified the target IP within the local subnet.

![IMG_0001](https://github.com/user-attachments/assets/27c18f5c-a492-451e-9bcb-7c26a4dce983)

* **Service Profiling**: A comprehensive service scan (`nmap -sCV -T4`) confirmed that **Port 80 (HTTP)** was the only open port, identifying the primary attack surface.

### **2. Advanced Directory & Parameter Fuzzing (`bubble_scan`)**
* **Aggressive Fuzzing**: Initial scans with standard wordlists yielded minimal results.

<img width="778" height="704" alt="IMG_0002" src="https://github.com/user-attachments/assets/1643e951-c524-4c56-a39a-8689069d2f48" />

* **Endpoint Discovery**: Use of DirBuster with a customized dictionary eventually revealed the hidden entry point `/exploit.html`.

![IMG_0003](https://github.com/user-attachments/assets/c24c6fb3-242d-4cb8-bd6a-7b753dea385c)

* **Hidden Portal Discovery**: Manual reconnaissance and intelligence siphoning identified the `/enter_network` directory, which served as an administrative gateway.

### **3. Exploitation & Lateral Movement**
* **Upload Form Bypass**: The `/exploit.html` form was misconfigured to point to `localhost`.
* **Manual Request Crafting**: This was bypassed using a crafted `curl -X POST` request targeting the backend handler, `profile.php`.

<img width="1234" height="806" alt="IMG_0006" src="https://github.com/user-attachments/assets/fc7121a4-3c2f-4ef4-af13-29e649fbc2ac" />

* **Information Disclosure**: Interacting with the backend directly triggered a partial flag disclosure: **`FLAG{N7`**.
* **Blind SQL Injection**: The login form at `/enter_network` was found to be vulnerable to SQL injection. 
* **Database Targeting**: Using `sqlmap` with `--level 3 --risk 3`, a time-based blind injection was confirmed on the `user` parameter.

![IMG_0007](https://github.com/user-attachments/assets/95a2d0a5-df9e-4a55-9f9b-facddd54961e)


### **4. Post-Exploitation & Data Siphoning (`bubble_siphon`)**
* **Database Exfiltration**: `sqlmap` was used to dump the **Machine** database and its corresponding `login` table.
* **Loot Extraction**: The final flag and administrator credentials were recovered from the password field of the database.

<img width="1504" height="379" alt="IMG_0008" src="https://github.com/user-attachments/assets/c79f9a07-1bfd-4cc3-b950-35f652f23ba7" />

* **Siphon Script Deployment**: The **`bubble_siphon.sh`** framework was prepared to profile the `www-data` identity and scavenge the `/var/www/` directory for configuration secrets such as `.env` and `config.php` files.

---

## 📂 Featured Tools
* **`bubble_siphon.sh`**: Custom post-exploitation framework for scavenging system secrets and SSH keys.
* **SQLmap**: Utilized for automated database siphoning and credential recovery.
* **FFUF/DirBuster**: Deployed for endpoint discovery and parameter fuzzing.

## 🏆 Final Flag
> **`FLAG{N7:KSA_01}`**
