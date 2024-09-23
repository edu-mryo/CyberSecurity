![](assets/Pasted%20image%2020240916161809.png)
# Machine Name: Bank
**Platform:** Hack The Box
**Difficulty:** Easy
**OS:** Linux

## Introduction

Bank is a relatively simple machine, however proper web enumeration is key to finding the necessary data for entry. There also exists an unintended entry method, which many users find before the correct data is located.
## Recon

* **Nmap Scan:** 
The scan shows there are three open ports; SSH (20), domain (53) and http (80). Port 53 seems to be [DNS port](https://www.cbtnuggets.com/common-ports/what-is-port-53). So , it does not look important right now. This leaves me with port 80, a http
```shell
# Nmap 7.94SVN scan initiated Mon Sep 16 16:18:53 2024 as: nmap -T4 -Pn -sVC -oN /home/markryo/Desktop/bank.txt target.htb
Nmap scan report for target.htb (10.129.29.200)
Host is up (0.18s latency).
Not shown: 996 closed tcp ports (conn-refused)
PORT      STATE    SERVICE VERSION
22/tcp    open     ssh     OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   1024 08:ee:d0:30:d5:45:e4:59:db:4d:54:a8:dc:5c:ef:15 (DSA)
|   2048 b8:e0:15:48:2d:0d:f0:f1:73:33:b7:81:64:08:4a:91 (RSA)
|   256 a0:4c:94:d1:7b:6e:a8:fd:07:fe:11:eb:88:d5:16:65 (ECDSA)
|_  256 2d:79:44:30:c8:bb:5e:8f:07:cf:5b:72:ef:a1:6d:67 (ED25519)
53/tcp    open     domain  ISC BIND 9.9.5-3ubuntu0.14 (Ubuntu Linux)
| dns-nsid: 
|_  bind.version: 9.9.5-3ubuntu0.14-Ubuntu
80/tcp    open     http    Apache httpd 2.4.7 ((Ubuntu))
|_http-server-header: Apache/2.4.7 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
48080/tcp filtered unknown
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon Sep 16 16:19:27 2024 -- 1 IP address (1 host up) scanned in 33.37 seconds
```

- **http enumeration**:
From the nmap report, the open port 80 is an apache server . I confirmed the status using `curl` command, which returned the apache server page. It's worth checking if I can find something from the directory enumeration.


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