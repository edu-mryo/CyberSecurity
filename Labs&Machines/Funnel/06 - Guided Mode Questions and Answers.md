##### How many TCP ports are open?
simple nmap enumeration gave me 2 output, which was `ftp` and `ssh`
 ==**2**==

##### What is the name of the directory that is available on the FTP server?
The nmap enumeration found that the ftp can be Anonymously login allowed ([[01 - Enumeration#nmap]]).
==**mail_backup**==

##### What is the default account password that every new member on the "Funnel" team should change as soon as possible?
See [[01 - Enumeration#Deep dive retrieved files from 01 - Enumeration FTP]] for details
==**funnel123#!#**==

##### Which user has not changed their default password yet?
Per [[01 - Enumeration#SSH Enumeration]], it was `christine`

##### Which service is running on TCP port 5432 and listens only on localhost?
