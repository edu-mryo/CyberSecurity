# Funnel Enumeration

## nmap

I initially ran the following command: Looks like we have 2 ports open. 

```bash
$ nmap funnel.htb

Nmap scan report for funnel.htb (10.129.228.195)
Host is up (0.24s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh
```


```bash
$ nmap -sC -sV -oA nmap/funnel 10.0.0.1
Nmap scan report for funnel.htb (10.129.228.195)
Host is up (0.24s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
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
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxr-xr-x    2 ftp      ftp          4096 Nov 28  2022 mail_backup
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 48add5b83a9fbcbef7e8201ef6bfdeae (RSA)
|   256 b7896c0b20ed49b2c1867c2992741c1f (ECDSA)
|_  256 18cd9d08a621a8b8b6f79f8d405154fb (ED25519)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```


Key Findings:
- ftp server on port 21
- ssh server on port 22

## FTP

The `nmap` gave me details on FTP anonymous login. 

```bash
$ ftp funnel.htb
Connected to funnel.htb.
220 (vsFTPd 3.0.3)
Name (funnel.htb:root): anonymous
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
drwxr-xr-x    2 ftp      ftp          4096 Nov 28  2022 mail_backup
226 Directory send OK.
ftp> cd mail_backup
250 Directory successfully changed.
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-r--r--    1 ftp      ftp         58899 Nov 28  2022 password_policy.pdf
-rw-r--r--    1 ftp      ftp           713 Nov 28  2022 welcome_28112022
226 Directory send OK.
ftp> mget *
mget password_policy.pdf? 
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for password_policy.pdf (58899 bytes).
226 Transfer complete.
58899 bytes received in 0.48 secs (120.8505 kB/s)
mget welcome_28112022? 
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for welcome_28112022 (713 bytes).
226 Transfer complete.
713 bytes received in 0.00 secs (1006.1981 kB/s)

```

The FTP has a directory named `mail_backup` . Under the dir, I found two files which I would like to deepdive.

Using `mget *` , I bulk downloaded the files.

Key findings:
- FTP allowed anonymous login using `anonymous` username. Password is blank
- The FTP had a directory called `mail_backup` and it contained two files.

## Deep dive retrieved files from [[01 - Enumeration#FTP]]

##### PDF contents

![[password_policy.png]]
The password policy pdf has the general guideline on creating a secure password. It also hints that the default password is ==`funnel123#!#`== .

##### Email Content

```bash
$ cat welcome_28112022 
Frome: root@funnel.htb
To: optimus@funnel.htb albert@funnel.htb andreas@funnel.htb christine@funnel.htb maria@funnel.htb
Subject:Welcome to the team!

Hello everyone,
We would like to welcome you to our team. 
We think you’ll be a great asset to the "Funnel" team and want to make sure you get settled in as smoothly as possible.
We have set up your accounts that you will need to access our internal infrastracture. Please, read through the attached password policy with extreme care.
All the steps mentioned there should be completed as soon as possible. If you have any questions or concerns feel free to reach directly to your manager. 
We hope that you will have an amazing time with us,
The funnel team. 
```

The welcome email was a copy of email which seems to be an onboarding email to new employees. This is good because its exposing the `To:` section and we can try to see if anyone in the group has forgotten to change their account password. 

## SSH Enumeration
