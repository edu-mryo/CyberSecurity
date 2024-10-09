![](assets/Pasted%20image%2020241008230035.png)
# Machine Name: Caving
**Platform:** Hack The Box,
**Difficulty:** Very Easy
**OS:** Linux

## Introduction

Caving is an Easy Linux machine that showcases an account takeover vulnerability ([CVE-2023-32707](https://nvd.nist.gov/vuln/detail/CVE-2023-32707)) in `Splunk`. A vulnerable Splunk server can be exploited by a low-privileged user with the `edit_user` permission. As a result a low-privileged user can escalate their privileges to administrator account. Having administrator privileges it is possible to create an app which can result in executing commands on the server.lenge.

## Recon

* **Nmap Scan:** `└──╼ [★]$ nmap caving.htb -T4 -Pn -sVC -oN ~/Desktop/caving.txt`

```bash
# Nmap 7.94SVN scan initiated Tue Oct  8 20:35:36 2024 as: nmap -T4 -Pn -sVC -oN /home/pochify/Desktop/caving.txt caving.htb
Nmap scan report for caving.htb (10.129.230.97)
Host is up (0.068s latency).
Not shown: 997 closed tcp ports (reset)
PORT     STATE SERVICE  VERSION
22/tcp   open  ssh      OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 48:ad:d5:b8:3a:9f:bc:be:f7:e8:20:1e:f6:bf:de:ae (RSA)
|   256 b7:89:6c:0b:20:ed:49:b2:c1:86:7c:29:92:74:1c:1f (ECDSA)
|_  256 18:cd:9d:08:a6:21:a8:b8:b6:f7:9f:8d:40:51:54:fb (ED25519)
8000/tcp open  http     Splunkd httpd
| http-robots.txt: 1 disallowed entry 
|_/
| http-title: Site doesn\'t have a title (text/html; charset=UTF-8).
|_Requested resource was http://caving.htb:8000/en-US/account/login?return_to=%2Fen-US%2F
|_http-server-header: Splunkd
8089/tcp open  ssl/http Splunkd httpd
| ssl-cert: Subject: commonName=SplunkServerDefaultCert/organizationName=SplunkUser
| Not valid before: 2023-11-14T14:45:02
|_Not valid after:  2026-11-13T14:45:02
|_http-title: splunkd
| http-robots.txt: 1 disallowed entry 
|_/
|_http-server-header: Splunkd
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

The machine seems to have active ports in `8000` and `8089` and they are Splunk.
- What is Splunk ? : Seems to be a log collecting platform.
	- [Japanese article](https://qiita.com/frozencatpisces/items/59703f57ce88ec936255)
	- [Official website](https://www.splunk.com/en_us/blog/learn/what-splunk-does.html)



* **Directory Enumeration (if applicable):**
    * Tools used (Gobuster, Dirbuster, etc.)
    * Interesting directories or files discovered.
* **Other Recon Tools (if applicable):**
    * Mention any additional recon techniques or tools used.

## Vulnerability Identification

* **Vulnerability 1:** vuln scan `nmap caving.htb --script vuln` 

```bash

PORT     STATE SERVICE
22/tcp   open  ssh
8000/tcp open  http-alt
| http-slowloris-check: 
|   VULNERABLE:
|   Slowloris DOS attack
|     State: LIKELY VULNERABLE
|     IDs:  CVE:CVE-2007-6750
|       Slowloris tries to keep many connections to the target web server open and hold
|       them open as long as possible.  It accomplishes this by opening connections to
|       the target web server and sending a partial request. By doing so, it starves
|       the http server\'s resources causing Denial Of Service.
|       
|     Disclosure date: 2009-09-17
|     References:
|       http://ha.ckers.org/slowloris/
|_      https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2007-6750
| http-enum: 
|_  /robots.txt: Robots file
8089/tcp open  unknown

```


    * Describe the identified vulnerability in detail.
    * ![Screenshot or PoC]
    * Explain the exploitation process.
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