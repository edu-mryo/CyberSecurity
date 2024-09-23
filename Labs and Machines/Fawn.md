![](assets/Pasted%20image%2020240923145936.png)
# Machine Name: Fawn
**Platform:** Hack The Box
**Difficulty:** Very Easy 
**OS:** Linux

## Introduction

* One of the first fundamental machines at HTB. Very very basic
## Recon

* **Nmap Scan:** `nmap fawn.htb -T4 -Pn -sVC -oN ~/Desktop/nmap/fawn.txt`
```bash
$ cat nmap/fawn.txt     
# Nmap 7.94SVN scan initiated Mon Sep 23 14:59:09 2024 as: nmap -T4 -Pn -sVC -oN /home/markryo/Desktop/nmap/fawn.txt fawn.htb
Nmap scan report for fawn.htb (10.129.1.14)
Host is up (0.18s latency).
Not shown: 992 closed tcp ports (conn-refused)
PORT      STATE    SERVICE              VERSION
21/tcp    open     ftp                  vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.10.14.2
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 3
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0              32 Jun 04  2021 flag.txt
1050/tcp  filtered java-or-OTGfileshare
1494/tcp  filtered citrix-ica
1687/tcp  filtered nsjtp-ctrl
1755/tcp  filtered wms
5190/tcp  filtered aol
5907/tcp  filtered dsd
55600/tcp filtered unknown
Service Info: OS: Unix
```

- Most ports are closed aside from FTP. Seems like anonymous login is allowed. 

## Vulnerability Identification

* **Vulnerability 1:** anonymous login allowed:
The FTP has the FTP anonymous allowed on, which means anyone can access the FTP using the anonymous login. Looking into the FTP, I was able to find the flag.txt , which is what we were exactly looking for. 

```bash
$ ftp fawn.htb                                          
Connected to fawn.htb.
220 (vsFTPd 3.0.3)
Name (fawn.htb:kali): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||21139|)
150 Here comes the directory listing.
-rw-r--r--    1 0        0              32 Jun 04  2021 flag.txt
226 Directory send OK.
ftp> get flag.txt
local: flag.txt remote: flag.txt
229 Entering Extended Passive Mode (|||59955|)
150 Opening BINARY mode data connection for flag.txt (32 bytes).
100% |***************************************************************************************************************|    32       84.91 KiB/s    00:00 ETA
226 Transfer complete.
32 bytes received in 00:00 (0.16 KiB/s)
ftp> exit
221 Goodbye.
```

## Lessons Learned

- Always check anonymous login for FTP
## Conclusion

- The machine has one open port in FTP (21). 
- The FTP was anonymous login allowed. 
- The FTP had flag.txt
- Very very easy pwn

**Happy hacking!**