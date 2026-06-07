# File Analysis - Scanning Result
## Vulnerability Analysis Lab - W6 & W7

# Question 1: Analyse packet1.pcap and find the flag.

After reviewing the network traffic contained within `packet1.pcap`, the relevant packets were filtered and examined using Wireshark. The analysis focused on identifying suspicious protocols, unusual communication patterns, and embedded data that could contain the challenge flag. By inspecting the packet contents and reconstructing the transmitted information, the hidden flag was successfully identified.

![Question 1](https://github.com/user-attachments/assets/17630631-6db1-4e2b-b264-df1ddb9814c0)

![Question 1 Result](https://github.com/user-attachments/assets/71cd5ebc-167f-483c-a166-30f51f8ed815)

---

# Question 2: Analyse packet2.pcap and find the flag.

The `packet2.pcap` file was examined using Wireshark to identify any suspicious activity and extract hidden information. Through packet inspection and protocol analysis, the relevant traffic was isolated and reviewed. The investigation revealed the flag embedded within the captured communication data.

![Question 2](https://github.com/user-attachments/assets/80fb7098-c1de-4fc3-bf48-5cc3bebaf85e)

---

# Question 3: Interpret an Nmap Output

```text
PORT     STATE SERVICE     VERSION
21/tcp   open ftp         vsftpd 2.3.4
22/tcp   open ssh         OpenSSH 5.3p1
80/tcp   open http        Apache 2.2.8
139/tcp  open netbios-ssn
445/tcp  open microsoft-ds Windows 7 Professional 7601 Service Pack 1
```

## 1. What can an attacker do with each port?

### i. Port 21 (FTP – vsftpd 2.3.4)

- Upload, download, modify, or delete files stored on the FTP server.
- Perform brute-force attacks to obtain valid credentials.
- Exploit the known backdoor vulnerability present in vsftpd 2.3.4.
- Gain unauthorized root-level access without authentication.
- Access files anonymously if anonymous login is enabled.

### ii. Port 22 (SSH – OpenSSH 5.3p1)

- Conduct brute-force attacks against SSH accounts.
- Obtain remote shell access after successful authentication.
- Use SSH tunnelling and port forwarding to access internal services.
- Establish persistence by deploying backdoors.
- Transfer files securely using SCP or SFTP.

### iii. Port 80 (HTTP – Apache 2.2.8)

- Exploit web application vulnerabilities such as SQL Injection, XSS, LFI, RFI, and Command Injection.
- Access sensitive information through directory listing misconfigurations.
- Upload malicious web shells for remote control.
- Modify website content or deface web pages.
- Exploit known Apache vulnerabilities to achieve denial-of-service or remote code execution.

### iv. Port 139 (NetBIOS Session Service)

- Enumerate hostnames, workgroups, and shared resources.
- Gather information about users and network services.
- Exploit weaknesses in legacy NetBIOS implementations.
- Launch SMB relay attacks.
- Perform man-in-the-middle attacks against NetBIOS name resolution.

### v. Port 445 (Microsoft-DS – Windows 7 SP1)

- Access shared files, folders, and printers.
- Exploit SMB vulnerabilities such as EternalBlue (MS17-010).
- Execute pass-the-hash attacks.
- Extract credentials using tools such as Mimikatz.
- Move laterally throughout the network.
- Deploy ransomware or malware.
- Enumerate users, groups, and system information.

---

## 2. What vulnerabilities are likely present based on the version?

| Service | Version | Potential Vulnerabilities |
|----------|----------|--------------------------|
| FTP | vsftpd 2.3.4 | **CVE-2011-2523** – Backdoor vulnerability allowing remote command execution and root access. |
| SSH | OpenSSH 5.3p1 | **CVE-2010-5107** – Username enumeration. **CVE-2008-5161** – Plaintext recovery attack under specific conditions. |
| HTTP | Apache 2.2.8 | **CVE-2011-3192** – Apache Killer Denial-of-Service. **CVE-2012-0052** – HTTP Request Smuggling. Additional vulnerabilities may exist in outdated modules. |
| SMB | Windows 7 SP1 | **MS17-010 (EternalBlue)** – Remote code execution. SMB relay attacks and other legacy SMB weaknesses. |

---

## 3. Which one is the highest risk and why?

The most critical security concern is Port 21 running **vsftpd version 2.3.4**. This version contains a documented backdoor vulnerability that can be triggered by supplying a specially crafted username containing the characters `:)`.

Once activated, the service opens Port 6200 and grants root-level access to the attacker without requiring authentication. Because publicly available exploit code and Metasploit modules exist for this vulnerability, successful exploitation is relatively straightforward.

Although the SMB service on Port 445 is also vulnerable to serious attacks such as EternalBlue, the simplicity and reliability of the vsftpd backdoor make Port 21 the most immediate threat.

---

## 4. What attack path can be built from this?

An attacker would begin by conducting reconnaissance using Nmap to identify active services running on the target system. The scan reveals FTP, SSH, HTTP, NetBIOS, and SMB services.

The attacker may first target the vulnerable FTP service running vsftpd 2.3.4. By exploiting the backdoor vulnerability, root-level access can be obtained through Port 6200.

Once administrative privileges are acquired, the compromised host can be used as a pivot point to discover and attack additional systems within the network. The attacker may identify a Windows 7 system exposing SMB services and exploit the MS17-010 vulnerability to obtain SYSTEM-level access.

Following successful exploitation, credentials can be harvested, additional hosts can be compromised through lateral movement, and sensitive information can be exfiltrated. Alternatively, the attacker could exploit vulnerabilities in the Apache web service to gain an initial foothold before escalating privileges and moving deeper into the network.

---

## 5. What should be the remediation?

### i. Port 21 (FTP – vsftpd 2.3.4)

- Upgrade to the latest supported version of vsftpd.
- Replace FTP with SFTP whenever possible.
- Disable anonymous login.
- Restrict FTP access using firewall rules.
- Continuously monitor FTP logs for suspicious activity.

### ii. Port 22 (SSH – OpenSSH 5.3p1)

- Upgrade OpenSSH to the latest stable release.
- Disable direct root login.
- Enforce key-based authentication.
- Implement brute-force protection tools such as Fail2Ban.
- Enable multi-factor authentication (MFA).

### iii. Port 80 (HTTP – Apache 2.2.8)

- Upgrade Apache to a supported version.
- Remove unnecessary modules.
- Deploy a Web Application Firewall (WAF).
- Enforce HTTPS for all communications.
- Validate and sanitize all user inputs.
- Disable directory browsing.
- Keep web applications updated.

### iv. Port 139 (NetBIOS)

- Disable NetBIOS where it is no longer required.
- Block Port 139 using firewall policies.
- Migrate to modern SMB implementations.

### v. Port 445 (Microsoft-DS – Windows 7 SP1)

- Upgrade legacy systems to supported operating systems.
- Apply the MS17-010 security patch.
- Disable SMBv1.
- Restrict access to Port 445.
- Isolate legacy devices using network segmentation.
- Deploy Endpoint Detection and Response (EDR) solutions.
- Monitor SMB traffic for suspicious activity.

---

# Question 4: Identify the Operating System (OS Fingerprinting) Using TTL

## Sample 1

![TTL Analysis 1](https://github.com/user-attachments/assets/c372881f-36ef-4030-9cc6-dda70a0de7ad)

### Answer

**Linux / Unix-Based Operating System**

The observed TTL value is consistent with Linux and Unix-based operating systems, which typically use a default TTL value of 64. The captured packet indicates a TTL value close to this default, suggesting the target system is likely running Linux or another Unix-based platform.

---

## Sample 2

![TTL Analysis 2](https://github.com/user-attachments/assets/5e71689b-9069-4137-a69d-ac2a891ecf15)

### Answer

**Microsoft Windows Operating System**

The captured packet shows a TTL value of approximately 125. Since Windows systems generally use a default TTL value of 128, the reduction can be attributed to network hops between the source and destination. This indicates that the target is likely running a Windows operating system.

---

## Sample 3

![TTL Analysis 3](https://github.com/user-attachments/assets/1c6087c4-5006-4f27-bef7-0f6d4c2ded53)

### Answer

**Microsoft Windows Operating System**

The observed TTL value closely matches the default Windows TTL value of 128. Based on this information, the operating system is most likely a Windows-based system.

---

# Question 5: Analyse the Nessus File

The Nessus scan report identified a critical vulnerability known as **Ghostcat**, which affects Apache Tomcat servers that expose the Apache JServ Protocol (AJP) service. The vulnerability allows attackers to read sensitive files and, under certain configurations, achieve remote code execution.

Ghostcat is tracked as **CVE-2020-1938** and has a CVSS score of **9.8**, making it a critical security issue requiring immediate remediation.

![Ghostcat Scan Result](https://github.com/user-attachments/assets/15bd6cbe-8395-49c7-b8ad-8163b57b4058)

![Ghostcat Details](https://github.com/user-attachments/assets/f07f2141-b90e-4fff-aa9a-ed01ee8e9cbc)

![Ghostcat Vulnerability Information](https://github.com/user-attachments/assets/92d2bcbd-7acc-4c74-9a71-4096f227cc9c)

![Ghostcat Additional Information](https://github.com/user-attachments/assets/21d74a0a-7052-4322-87d1-6837111252ff)

## 1. What is the affected port number?

**Answer:** Port **8009**

The vulnerability affects the Apache JServ Protocol (AJP) service listening on Port 8009.

---

## 2. What is the affected protocol?

**Answer:** Apache JServ Protocol (**AJP**)

AJP is a protocol used to facilitate communication between Apache web servers and Apache Tomcat application servers.

---

## 3. What is the CVSS score of the vulnerability?

**Answer:** **9.8 (Critical)**

The vulnerability is classified as critical due to the significant impact it may have on confidentiality, integrity, and availability.

---

## 4. Can you find any exploit related to this vulnerability?

**Answer:** Yes

Public exploit code for Ghostcat is available, and the Nessus report confirms that exploitation techniques exist. Attackers can leverage these exploits to access sensitive files or compromise vulnerable systems.

---

## 5. Find the CVE for this vulnerability.

**Answer:**

- **CVE-2020-1938** (Ghostcat)
- **CVE-2020-1745** (Related vulnerability identified within the Nessus report)

These vulnerabilities affect Apache Tomcat configurations that expose the AJP connector without proper security controls.

---

# Conclusion

The analysis demonstrates how network reconnaissance and vulnerability assessment tools such as Wireshark, Nmap, and Nessus can be used to identify security weaknesses within a target environment. The investigation revealed several outdated services and critical vulnerabilities, including the vsftpd 2.3.4 backdoor, Windows SMB weaknesses, and the Ghostcat vulnerability affecting Apache Tomcat. Proper patch management, service hardening, network segmentation, and continuous monitoring are essential to reduce the attack surface and protect systems from compromise.
