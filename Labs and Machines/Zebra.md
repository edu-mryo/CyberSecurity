![](assets/Pasted%20image%2020241009223750.png)
# Machine Name: Zebra
**Platform:** Hack The Box
**Difficulty:** Very Easy
**OS:** [Linux, Windows, etc.]


## Introduction

Zebra is a very easy Linux machine that showcases a recently disclosed local privilege escalation vulnerability (`CVE-2022-37393`) in `Zimbra Collaboration`, which allows the zimbra user to elevate their privileges due to an insecure default sudo configuration which permits the execution of zmslapd as root with arbitrary parameters.

## Recon

* **Nmap Scan:** `nmap zebra.htb -Pn -T4 -sVC -oN ~/Desktop/nmap/zebra.txt `

```bash
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-09 22:40 JST
Nmap scan report for zebra.htb (10.129.228.77)
Host is up (0.22s latency).
Not shown: 997 filtered tcp ports (no-response)
PORT     STATE SERVICE  VERSION
22/tcp   open  ssh      OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 48:ad:d5:b8:3a:9f:bc:be:f7:e8:20:1e:f6:bf:de:ae (RSA)
|   256 b7:89:6c:0b:20:ed:49:b2:c1:86:7c:29:92:74:1c:1f (ECDSA)
|_  256 18:cd:9d:08:a6:21:a8:b8:b6:f7:9f:8d:40:51:54:fb (ED25519)
80/tcp   open  http     nginx 1.18.0 (Ubuntu)
|_http-title: HackTheBox WebShell
|_http-server-header: nginx/1.18.0 (Ubuntu)
8443/tcp open  ssl/http Zimbra http config
| ssl-cert: Subject: commonName=zebra.htb
| Subject Alternative Name: DNS:zebra.htb
| Not valid before: 2022-09-27T06:53:32
|_Not valid after:  2027-09-26T06:53:32
|_http-title: Zimbra Web Client Sign In
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 42.51 seconds

```

* **Directory Enumeration (if applicable):**
    * Tools used (Gobuster, Dirbuster, etc.)
    * Interesting directories or files discovered.
* **Other Recon Tools (if applicable):**
    * Mention any additional recon techniques or tools used.

## Vulnerability Identification

* **Vulnerability 1:**
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