---

title: "TryHackMe Advent Of Cyber 2024"
date: 2024-12-27 10:00:00 +0300
categories: [Cybersecurity, Challenges, Learning]
---

The **TryHackMe Advent of Cyber 2024** was an exceptional learning experience that combined engaging challenges with real-world scenarios to enhance both foundational and advanced cybersecurity skills. In this post, I’ll provide a comprehensive overview of everything I learned through the daily challenges.

---

## Overview of the Challenges

The challenge was structured to introduce a variety of cybersecurity concepts in a hands-on, practical manner. Each day featured a new scenario, covering topics such as vulnerability scanning, incident response, and cryptography. Here’s a detailed breakdown of the technical lessons and skills I gained:

---

## Day 1: Introduction to Cybersecurity Tools

The challenge started with a primer on essential cybersecurity tools:

- **Nmap**: Used to perform network scanning and identify open ports and services.
- **Wireshark**: Leveraged for packet analysis to detect suspicious traffic.
- **Burp Suite**: Explored for intercepting and manipulating HTTP requests in web applications.

### Key Takeaways:

- Learned the importance of using tools in combination to gain a complete view of a system’s vulnerabilities.
- Understood how to interpret Nmap output and analyze network traffic using Wireshark filters.

---

## Day 2: Vulnerability Scanning and Exploitation

This day focused on discovering and exploiting vulnerabilities:

- **Open ports and services**: Used Nmap to identify misconfigured services.
- **Exploitation**: Explored how to leverage publicly available exploits using Metasploit and manual methods.

### Key Takeaways:

- Gained insights into the common vulnerabilities found in outdated software.
- Practiced manual exploitation for a deeper understanding of the attack vectors.

---

## Day 3: Phishing and Social Engineering

This challenge simulated a phishing attack:

- Analyzed a phishing email to extract malicious URLs.
- Used tools like **CyberChef** to decode obfuscated payloads.
- Practiced identifying phishing indicators in email headers.

### Key Takeaways:

- Improved my ability to identify phishing attempts through header analysis.
- Learned how attackers obfuscate payloads to bypass detection.

---

## Day 4: Cryptography Basics

Focused on understanding encryption and decryption:

- Learned the differences between symmetric and asymmetric cryptography.
- Practiced decrypting messages using a private key.
- Explored hashing algorithms and their use in data integrity.

### Key Takeaways:

- Understood how cryptographic protocols like RSA and AES are implemented.
- Gained hands-on experience with decrypting messages and verifying hashes.

---

## Day 5: Web Application Security

This day delved into web application vulnerabilities:

- Identified and exploited **SQL Injection** vulnerabilities.
- Explored **Cross-Site Scripting (XSS)** attacks.
- Used Burp Suite to intercept and manipulate web traffic.

### Key Takeaways:

- Gained practical knowledge of how input validation flaws can be exploited.
- Learned secure coding practices to prevent common web vulnerabilities.

---

## Day 6: Threat Hunting

This challenge introduced threat-hunting techniques:

- Analyzed system logs to identify anomalies.
- Detected malicious PowerShell commands using event logs.
- Practiced filtering relevant events in SIEM tools.

### Key Takeaways:

- Learned to correlate logs to identify patterns of malicious activity.
- Understood the importance of baselining normal behavior to detect anomalies.

---

## Day 7: Incident Response

Focused on responding to a simulated breach:

- Conducted a root cause analysis of the attack.
- Implemented remediation steps, including revoking compromised credentials and patching vulnerabilities.
- Documented findings in an incident response report.

### Key Takeaways:

- Strengthened my ability to respond effectively to incidents.
- Learned the importance of documentation for improving future defenses.

---

## Day 8: Privilege Escalation

This challenge explored escalating privileges on a compromised system:

- Identified misconfigured file permissions and scheduled tasks.
- Exploited vulnerabilities to gain administrator-level access.

### Key Takeaways:

- Learned techniques to identify privilege escalation opportunities.
- Understood how to secure systems by tightening permissions and monitoring critical tasks.

---

## Day 9: Network Forensics

Analyzed a network traffic capture file:

- Used Wireshark to extract credentials transmitted over HTTP.
- Identified signs of data exfiltration using unusual traffic patterns.

### Key Takeaways:

- Practiced analyzing network traffic for signs of compromise.
- Gained insights into the risks of unencrypted communications.

---

## Day 10: Active Directory Security

Focused on attacking and defending Active Directory environments:

- Enumerated AD users and groups using tools like **BloodHound** and **SharpHound**.
- Practiced common attacks like **Kerberoasting**.

### Key Takeaways:

- Learned to identify misconfigurations in AD environments.
- Gained insights into defending against AD attacks.

---

## Day 11: Malware Analysis Basics

Explored how to analyze suspicious files:

- Used **strings** to extract readable text from binaries.
- Explored **dynamic analysis** techniques in a sandbox environment.

### Key Takeaways:

- Learned to identify potential indicators of compromise in malicious binaries.
- Gained an understanding of the static and dynamic malware analysis process.

---

## Day 12: DNS Reconnaissance

This day focused on DNS-based reconnaissance techniques:

- Practiced querying DNS records with tools like **dig** and **nslookup**.
- Explored subdomain enumeration using tools like **Sublist3r**.

### Key Takeaways:

- Understood the importance of securing DNS records to prevent data leakage.
- Gained experience with common tools for DNS reconnaissance.

---

## Day 13: Password Cracking

Focused on cracking password hashes:

- Used **John the Ripper** and **Hashcat** to crack common hash types.
- Explored techniques for creating strong passwords.

### Key Takeaways:

- Learned the weaknesses of common password hashes.
- Gained insights into building effective password policies.

---

## Day 14: Log Analysis

This day focused on analyzing system logs for anomalies:

- Identified unusual login attempts in SSH logs.
- Used **grep** and **awk** to filter relevant log entries.

### Key Takeaways:

- Strengthened log parsing and analysis skills.
- Learned to spot patterns indicative of brute-force attacks.

---

## Day 15: Cloud Security Basics

Explored the fundamentals of cloud security:

- Reviewed access control policies in AWS.
- Practiced identifying misconfigured S3 buckets.

### Key Takeaways:

- Understood common cloud security risks and mitigation strategies.
- Gained hands-on experience with securing cloud resources.

---

## Day 16: File Forensics

Focused on analyzing file metadata:

- Used tools like **ExifTool** to extract metadata from images.
- Identified hidden data within files using **binwalk**.

### Key Takeaways:

- Learned to uncover hidden information within file structures.
- Gained insights into file forensics techniques.

---

## Day 17: Network Segmentation

This day focused on securing network environments:

- Explored VLAN configurations to isolate traffic.
- Practiced configuring firewall rules for segmentation.

### Key Takeaways:

- Gained an understanding of the principles of network segmentation.
- Learned to implement secure communication paths between network zones.

---

## Day 18: Reverse Engineering Basics

Introduced basic reverse engineering techniques:

- Used **Ghidra** to analyze binary files.
- Practiced reconstructing code to understand functionality.

### Key Takeaways:

- Learned the fundamentals of reverse engineering.
- Gained insights into analyzing binary files for vulnerabilities.

---

## Day 19: Security Policy Enforcement

Focused on implementing security policies:

- Configured group policies for restricting access in Windows environments.
- Practiced applying least privilege principles.

### Key Takeaways:

- Strengthened knowledge of group policy configurations.
- Gained experience enforcing security best practices.

---

## Day 20: Threat Intelligence

This day introduced using threat intelligence for defense:

- Explored public threat intelligence feeds.
- Practiced integrating intelligence into SIEM systems.

### Key Takeaways:

- Understood the value of actionable threat intelligence.
- Gained hands-on experience leveraging intelligence feeds.

---

## Day 21: Red Team Tactics

Explored offensive security techniques:

- Practiced using **Cobalt Strike** for post-exploitation.
- Explored lateral movement techniques.

### Key Takeaways:

- Gained insights into red team methodologies.
- Learned to simulate real-world attacks effectively.

---

## Day 22: Blue Team Defenses

Focused on defending against attacks:

- Configured endpoint protection tools.
- Practiced detecting and mitigating common attack vectors.

### Key Takeaways:

- Strengthened detection and response skills.
- Learned to harden systems against known exploits.

---

## Day 23: Incident Management

This day emphasized managing incidents effectively:

- Practiced creating incident playbooks.
- Explored techniques for managing cross-team communications.

### Key Takeaways:

- Learned the importance of structured incident response plans.
- Gained insights into effective communication during incidents.

---

## Day 24: Capture the Flag (CTF)

The final day wrapped up with a CTF challenge:

- Combined skills from all previous days to solve complex scenarios.
- Demonstrated the ability to think critically and apply knowledge.

### Key Takeaways:

- Reinforced the importance of continuous learning and practice.
- Celebrated the culmination of an incredible cybersecurity journey.

<iframe src="/assets/pdfs/THM-TSFGCYITBQ.pdf" width="100%" height="600px"></iframe>

---

## Final Thoughts

The **Advent of Cyber 2024** was a perfect mix of fun and education. It provided an excellent opportunity to refresh my existing knowledge while diving into new topics. Challenges like these are invaluable for staying up-to-date in the ever-evolving field of cybersecurity. I highly recommend it to anyone, from beginners to seasoned professionals.

I look forward to applying these skills in real-world scenarios and participating in next year’s challenge. Until then, happy hacking! 🎉

---

*This post is part of Jad's Cybersecurity Blog.*
