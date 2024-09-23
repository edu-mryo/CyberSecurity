![](assets/Pasted%20image%2020240922102112.png)
# Machine Name: Valentine
**Platform:** Hack The Box
**Difficulty:** Easy ( Medium ? )
**OS:** Linux

## Introduction

Valentine is a very unique medium difficulty machine which focuses on the Heartbleed vulnerability, which had devastating impact on systems across the globe.

## Recon

* **Nmap Scan:**
```bash
# Nmap 7.94SVN scan initiated Sun Sep 22 12:03:17 2024 as: nmap -T4 -Pn -sVC -p- -oN /home/markryo/Desktop/valentine.txt valentine.htb
Warning: 10.129.232.135 giving up on port because retransmission cap hit (6).
Nmap scan report for valentine.htb (10.129.232.135)
Host is up (0.18s latency).
Not shown: 65393 closed tcp ports (conn-refused), 139 filtered tcp ports (no-response)
PORT    STATE SERVICE  VERSION
22/tcp  open  ssh      OpenSSH 5.9p1 Debian 5ubuntu1.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   1024 96:4c:51:42:3c:ba:22:49:20:4d:3e:ec:90:cc:fd:0e (DSA)
|   2048 46:bf:1f:cc:92:4f:1d:a0:42:b3:d2:16:a8:58:31:33 (RSA)
|_  256 e6:2b:25:19:cb:7e:54:cb:0a:b9:ac:16:98:c6:7d:a9 (ECDSA)
80/tcp  open  http     Apache httpd 2.2.22 ((Ubuntu))
|_http-title: Site doesn\'t have a title (text/html).
|_http-server-header: Apache/2.2.22 (Ubuntu)
443/tcp open  ssl/http Apache httpd 2.2.22
| ssl-cert: Subject: commonName=valentine.htb/organizationName=valentine.htb/stateOrProvinceName=FL/countryName=US
| Not valid before: 2018-02-06T00:45:25
|_Not valid after:  2019-02-06T00:45:25
|_http-server-header: Apache/2.2.22 (Ubuntu)
|_http-title: Site doesn\'t have a title (text/html).
|_ssl-date: 2024-09-22T03:18:58+00:00; 0s from scanner time.
Service Info: Host: 10.10.10.136; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Sep 22 12:18:59 2024 -- 1 IP address (1 host up) scanned in 942.22 seconds
```

* **Directory Enumeration (if applicable):** ffuf

```bash
$ ffuf -w /usr/share/seclists/Discovery/Web-Content/common.txt:FUZZ -u http://valentine.htb/FUZZ -recursion -mc 200,301

        /\'___\  /\'___\           /\'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://valentine.htb/FUZZ
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/common.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,301
________________________________________________

decode                  [Status: 200, Size: 552, Words: 73, Lines: 26, Duration: 184ms]
dev                     [Status: 301, Size: 312, Words: 20, Lines: 10, Duration: 184ms]
[INFO] Adding a new job to the queue: http://valentine.htb/dev/FUZZ

encode                  [Status: 200, Size: 554, Words: 73, Lines: 28, Duration: 186ms]
index.php               [Status: 200, Size: 38, Words: 2, Lines: 2, Duration: 182ms]
index                   [Status: 200, Size: 38, Words: 2, Lines: 2, Duration: 184ms]
[INFO] Starting queued job on target: http://valentine.htb/dev/FUZZ

notes                   [Status: 200, Size: 227, Words: 33, Lines: 9, Duration: 184ms]
:: Progress: [4727/4727] :: Job [2/2] :: 215 req/sec :: Duration: [0:00:22] :: Errors: 0 ::
```
    
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