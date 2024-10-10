![](assets/Pasted%20image%2020241010225832.png)
# Machine Name: Blue
**Platform:** Hack The Box
**Difficulty:**  Easy
**OS:** Windows

## Introduction

Blue, while possibly the most simple machine on Hack The Box, demonstrates the severity of the EternalBlue exploit, which has been used in multiple large-scale ransomware and crypto-mining attacks since it was leaked publicly.

## Recon

* **Nmap Scan:** `nmap -Pn blue.htb -sVC -T4 -oN ~/Desktop/nmap/blue.txt`

```bash
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-10 23:04 JST
Stats: 0:01:00 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 33.33% done; ETC: 23:07 (0:01:42 remaining)
Nmap scan report for blue.htb (10.129.232.181)
Host is up (0.18s latency).
Not shown: 991 closed tcp ports (reset)
PORT      STATE SERVICE      VERSION
135/tcp   open  msrpc        Microsoft Windows RPC
139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds Windows 7 Professional 7601 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
49152/tcp open  msrpc        Microsoft Windows RPC
49153/tcp open  msrpc        Microsoft Windows RPC
49154/tcp open  msrpc        Microsoft Windows RPC
49155/tcp open  msrpc        Microsoft Windows RPC
49156/tcp open  msrpc        Microsoft Windows RPC
49157/tcp open  msrpc        Microsoft Windows RPC
Service Info: Host: HARIS-PC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: -19m56s, deviation: 34m35s, median: 1s
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2:1:0: 
|_    Message signing enabled but not required
| smb-os-discovery: 
|   OS: Windows 7 Professional 7601 Service Pack 1 (Windows 7 Professional 6.1)
|   OS CPE: cpe:/o:microsoft:windows_7::sp1:professional
|   NetBIOS computer name: HARIS-PC\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2024-10-10T15:06:02+01:00
| smb2-time: 
|   date: 2024-10-10T14:05:59
|_  start_date: 2024-10-10T13:59:47

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 150.58 seconds
```

* **Directory Enumeration (if applicable):**
    * Tools used (Gobuster, Dirbuster, etc.)
    * Interesting directories or files discovered.
* **Other Recon Tools (if applicable):**
    * Mention any additional recon techniques or tools used.

## Vulnerability Identification

* **Vulnerability 1:** vuln scan

```bash
Host script results:
| smb-vuln-ms17-010: 
|   VULNERABLE:
|   Remote Code Execution vulnerability in Microsoft SMBv1 servers (ms17-010)
|     State: VULNERABLE
|     IDs:  CVE:CVE-2017-0143
|     Risk factor: HIGH
|       A critical remote code execution vulnerability exists in Microsoft SMBv1
|        servers (ms17-010).
|           
|     Disclosure date: 2017-03-14
|     References:
|       https://blogs.technet.microsoft.com/msrc/2017/05/12/customer-guidance-for-wannacrypt-attacks/
|       https://technet.microsoft.com/en-us/library/security/ms17-010.aspx
|_      https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-0143
|_smb-vuln-ms10-061: NT_STATUS_OBJECT_NAME_NOT_FOUND
|_smb-vuln-ms10-054: false

```

* **Vulnerability 2 (if any):**
    * Repeat the same format for any additional vulnerabilities.

## Exploitation

* **Gaining Initial Access:**
    * Step-by-step explanation of how you exploited the vulnerability to gain initial access to the machine.
    * ```
      [Include any relevant code snippets or commands used]
      ```
* **Privilege Escalation (if applicable):**
    * Describe the privilege escalation technique used.
    * Provide details of any exploits or misconfigurations leveraged.
* **Proof of Access:**
    * ![Screenshot of flags or other evidence]

## Post-Exploitation

* **Further Enumeration:**
    * Any additional information gathered after gaining access (interesting files, configurations, etc.).
* **Lateral Movement (if applicable):**
    * Explain how you moved to other machines on the network (if any).

## Lessons Learned

* Key takeaways from the challenge.
* What you learned about the specific vulnerabilities or techniques used.
* Any areas where you could have improved your approach.

## Conclusion

* Summarize your overall experience with the machine.
* Highlight the challenges you faced and how you overcame them.
* Offer any advice to others attempting the same machine.

**Happy hacking!**