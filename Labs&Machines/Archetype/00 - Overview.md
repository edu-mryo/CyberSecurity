
# Archetyp Overview

![](assets/Pasted%20image%2020240629224537.png)

Machine Details:

| IP  | Hostname       | Operating System |
| --- | -------------- | ---------------- |
|     | pennyworth.htb | Windows          |

## Box Outline

In the cyber security industry, there is a way to identify, define, and catalog publicly disclosed vulnerabilities. That type of identification is called a CVE, which stands for Common Vulnerabilities and Exposures.

Post-analysis, each vulnerability is assigned a severity rating, called a CVSS score, ranging from 0 to 10, where 0 is considered Informational, and 10 is Critical. These scores are dependent on several factors, some of which being the level of CIA Triad compromise (Confidentiality, Integrity, Availability), the level of attack complexity, the size of the attack surface, and others.

One of the most well-known and most feared vulnerability types to find on your system is called an Arbitrary Remote Command Execution vulnerability.

In computer security, arbitrary code execution (ACE) is an attacker's ability to execute arbitrary commands or code on a target machine or in a target process. [..] A program designed to exploit such a vulnerability is called an arbitrary code execution exploit. The ability to trigger arbitrary code execution over a network (primarily via a wide-area network such as the Internet) is often called remote code execution (RCE).
In this example, we will be exploring precisely this typology of attack vectors.



**Enumeration**
Standard Enumeration using Nmap and Googling


**Exploitation**
Required some Google search on how to get shell over Jenkins ci/cd and gain access via `nc -lnvp {port}` .


**Privilege Escalation**
Not required.


**Post Exploitation**
n/a


## Timeline

You can use this section to track when you started and finished this machine, including when you gained a 'foothold' (low-privilege shell) on the box and began privilege escalation. Knowing how long it took can help to judge how long a machine may take on the exam.

| Date Started | Date of Foothold | Date Finished |
| ------------ | ---------------- | ------------- |
| 2024/6/13    | 2024/6/11        | 2024/6/11     |

## Tags
#Linux #Jenkins 