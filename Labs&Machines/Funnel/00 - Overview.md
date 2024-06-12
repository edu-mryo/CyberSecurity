
# Funnel Overview

![](assets/funnel_logo.png)

Machine Details:

| IP             | Hostname   | Operating System |
| -------------- | ---------- | ---------------- |
| 10.129.228.195 | funnel.htb | Windows 7 SP1    |
Suppose that you are working remotely, and you want to access a database that is only available on your company's internal network. To make the example more specific, let's say you wanted to access a PostgreSQL database that is is often used by businesses and organizations to store, manage, and retrieve data that is critical to their operations. PostgreSQL, also known as Postgres, is a powerful and open-source relational database management system (RDBMS). It is widely used for managing and storing large amounts of data due to its reliability, flexibility, and performance. Without tunneling, you would not be able to access these resources directly. However, by using tunneling, you can create a secure connection between your local machine and the internal network, allowing you to access the internal services as if you were on the network itself. This can be particularly useful for remote employees who need to access internal resources but do not have direct access to the company's network.
## Box Outline

Box outlines are useful for revisiting the box and seeing how you exploited it. They may help you find practical examples of exploits, or trigger you to think about what you could improve

**Enumeration**

A standard enumeration (nmap) will give us enough insights on this machine.

**Exploitation**

Local Port Forwarding 

**Privilege Escalation**

`christine` did not change his/her default password, which gave me access to the ssh and further understand the machine vulnerability.

**Post Exploitation**

Dynamic Port Forwarding works

## Timeline

You can use this section to track when you started and finished this machine, including when you gained a 'foothold' (low-privilege shell) on the box and began privilege escalation. Knowing how long it took can help to judge how long a machine may take on the exam.

| Date Started | Date of Foothold | Date Finished |
| ------------ | ---------------- | ------------- |
| 2024/6/11    | 2024/6/11        | 2024/6/12     |

## Tags

#Linux #SSH #Tunneling #LocalPortForwarding #postgresql