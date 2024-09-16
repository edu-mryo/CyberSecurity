![](assets/Pasted%20image%2020240916000429.png)
# Machine Name: Lame
**Platform:** Hack The Box
**Difficulty:** Easy
**OS:** Linux

## Introduction

* Lame is an easy Linux machine, requiring only one exploit to obtain root access. It was the first machine published on Hack The Box and was often the first machine for new users prior to its retirement.
## Recon

* **Nmap Scan:**
`nmap target.htb -Pn -sCV -T4 -oX ~/Desktop/lame.txt`

 I exported the data to lame.txt for better viewability (It is better to output into one place rather than nmapping repeatedly).

```bash
# Nmap 7.94SVN scan initiated Mon Sep 16 00:17:31 2024 as: nmap -T4 -Pn -sVC -oN /home/markryo/Desktop/lame.txt target.htb
Nmap scan report for target.htb (10.129.232.110)
Host is up (0.19s latency).
Not shown: 996 filtered tcp ports (no-response)
PORT    STATE SERVICE     VERSION
21/tcp  open  ftp         vsftpd 2.3.4
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 10.10.14.2
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      vsFTPd 2.3.4 - secure, fast, stable
|_End of status
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
22/tcp  open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
| ssh-hostkey: 
|   1024 60:0f:cf:e1:c0:5f:6a:74:d6:90:24:fa:c4:d5:6c:cd (DSA)
|_  2048 56:56:24:0f:21:1d:de:a7:2b:ae:61:b1:24:3d:e8:f3 (RSA)
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 3.0.20-Debian (workgroup: WORKGROUP)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_smb2-time: Protocol negotiation failed (SMB2)
|_clock-skew: mean: 2h00m45s, deviation: 2h49m56s, median: 35s
| smb-os-discovery: 
|   OS: Unix (Samba 3.0.20-Debian)
|   Computer name: lame
|   NetBIOS computer name: 
|   Domain name: hackthebox.gr
|   FQDN: lame.hackthebox.gr
|_  System time: 2024-09-15T11:18:50-04:00

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon Sep 16 00:18:47 2024 -- 1 IP address (1 host up) scanned in 76.13 seconds

```

## Vulnerability Identification

 Looking into recon I noticed two things.
1. They have FTP ( Port : 22 ) open with *allow anonymous login*  . This means I can access their FTP without obtaining credentials and use **username: anonymous password: anonymous**. Anonymous login was successful, but nothing valuable was salvageable.   

```bash
$ ftp target.htb                                                                      
Connected to target.htb.
220 (vsFTPd 2.3.4)
Name (target.htb:kali): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||23849|).
150 Here comes the directory listing.
226 Directory send OK.
ftp> pwd
Remote directory: /
ftp> cd
(remote-directory) ls
550 Failed to change directory.
ftp> cd ../
250 Directory successfully changed.
ftp> ls
229 Entering Extended Passive Mode (|||19635|).
150 Here comes the directory listing.
226 Directory send OK.
ftp> 
```

2. They also have an open `Samba smbd 3.0.20-Debian` in port 445
	- What is Samba ? [Here is the official doc](https://www.samba.org/samba/what_is_samba.html). It's a software that allows Unix-like OS to communicate with SMB 
	- I initially tried `smbclient` to see if there is anything worth to deepdive. I found a `sharename` called `tmp` with `oh noes!` comment, which looked suspicious.

```bash
$ smbclient -L target.htb      
Password for [WORKGROUP\kali]:
Anonymous login successful

        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        tmp             Disk      oh noes!
        opt             Disk      
        IPC$            IPC       IPC Service (lame server (Samba 3.0.20-Debian))
        ADMIN$          IPC       IPC Service (lame server (Samba 3.0.20-Debian))
Reconnecting with SMB1 for workgroup listing.
Anonymous login successful

        Server               Comment
$ smbclient \\\\target.htb\\tmp
Password for [WORKGROUP\kali]:
Anonymous login successful
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Mon Sep 16 10:40:25 2024
  ..                                 DR        0  Sat Oct 31 15:33:58 2020
  .ICE-unix                          DH        0  Mon Sep 16 00:07:39 2024
  vmware-root                        DR        0  Mon Sep 16 00:08:02 2024
  .X11-unix                          DH        0  Mon Sep 16 00:08:04 2024
  .X0-lock                           HR       11  Mon Sep 16 00:08:04 2024
  5640.jsvc_up                        R        0  Mon Sep 16 00:08:50 2024
  vgauthsvclog.txt.0                  R     1600  Mon Sep 16 00:07:38 2024

        ---------            -------

        Workgroup            Master
        ---------            -------
        WORKGROUP            LAME
```

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