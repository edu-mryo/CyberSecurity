##### How many TCP ports are open?
simple nmap enumeration gave me 2 output, which was `ftp` and `ssh`
-  **2**

##### What is the name of the directory that is available on the FTP server?
The nmap enumeration found that the ftp can be Anonymously login allowed ([[01 - Enumeration.md#nmap]]).
- **mail_backup**

##### What is the default account password that every new member on the "Funnel" team should change as soon as possible?
See [[01 - Enumeration.md#Deep dive retrieved files from 01 - Enumeration FTP]] for details
- **funnel123#!#**

##### Which user has not changed their default password yet?
- Per [[01 - Enumeration#SSH Enumeration]], it was **`christine`**

##### Which service is running on TCP port 5432 and listens only on localhost?
- **postgresql** ( see [[01 - Enumeration.md#Enumerating nmap again]])

##### Since you can't access the previously mentioned service from the local machine, you will have to create a tunnel and connect to it from your machine. What is the correct type of tunneling to use? remote port forwarding or local port forwarding?
- Local port forwarding is to access remote machine, Remote Port forwarding to allow remote machine to access your machine . For this, the answer is local port forwarding

##### What is the name of the database that holds the flag?
- secrets

##### Could you use a dynamic tunnel instead of local port forwarding? Yes or No.
- yes 

##### Submit the flag located in the database.
- cf2*********************

