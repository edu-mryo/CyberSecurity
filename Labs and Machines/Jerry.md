![[Pasted image 20240926073515.png]]
# Machine Name: Jerry
**Platform:** Hack The Box
**Difficulty:** [Easy, Medium, Hard]
**OS:** [Linux, Windows, etc.]

## Introduction

* Brief description of the machine and its overall theme.
* Mention your initial thoughts and approach to the challenge.

## Recon

* **Nmap Scan:**`nmap -T4 -Pn -sCV -oN Desktop/jerry.txt jerry.htb

```bash
# Nmap 7.94SVN scan initiated Wed Sep 25 17:31:48 2024 as: nmap -T4 -Pn -sCV -oN Desktop/jerry.txt jerry.htb
Nmap scan report for jerry.htb (10.129.136.9)
Host is up (0.24s latency).
Not shown: 999 filtered tcp ports (no-response)
PORT     STATE SERVICE VERSION
8080/tcp open  http    Apache Tomcat/Coyote JSP engine 1.1
|_http-server-header: Apache-Coyote/1.1
|_http-favicon: Apache Tomcat
|_http-title: Apache Tomcat/7.0.88
```

The usual nmap check gives me one open port in 8080, which looks to be running a JSP engine called Tomcat. 
- Details on what Apache Tomcat is in Japanese : [図解で解説！Apache, Tomcat ってなんなの？](https://qiita.com/tanayasu1228/items/11e22a18dbfa796745b5)
```text
You're likely familiar with Java Servlets and JavaServer Pages (JSP), the fundamental building blocks for creating dynamic web applications in Java. While you can write these components, they need a runtime environment to execute and interact with web clients. That's where Apache Tomcat comes into play.

Tomcat, at its core, is a servlet container adhering to the Java Servlet and JSP specifications. It provides the necessary environment for your servlets and JSPs to live and breathe. Think of it as a specialized web server tailored for Java web applications.

Here's a breakdown of Tomcat's key functionality:

- **Request Handling:** Tomcat listens for HTTP requests from clients (like web browsers). It parses these requests and routes them to the appropriate servlet or JSP within your web application.
- **Servlet Lifecycle Management:** Tomcat manages the lifecycle of your servlets, from instantiation and initialization to service and destruction. It handles the creation of threads, object pooling, and other resource management tasks.
- **JSP Compilation and Execution:** When a request comes in for a JSP, Tomcat compiles it into a servlet and then executes it. This allows you to embed dynamic content within your web pages using Java code.
- **Security:** Tomcat provides security features like authentication and authorization to protect your web application from unauthorized access.
- **Deployment:** Tomcat offers various deployment mechanisms, including WAR files, for easy packaging and deployment of your web applications.

Essentially, Tomcat acts as an intermediary between web clients and your Java web application, handling the underlying communication, execution, and management of your servlets and JSPs. It abstracts away the complexities of the web server environment, allowing you to focus on writing the logic for your application.

Think of it as a lightweight, Java-centric alternative to full-fledged application servers like JBoss or WebLogic. It's perfect for deploying and running moderately complex web applications without the overhead of a full-blown JEE server.
```

I also tried the vulnerability scan using nmap: `nmap --script vuln jerry.htb`

```bash
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-09-25 17:36 CDT
Stats: 0:00:25 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE Timing: About 47.57% done; ETC: 17:37 (0:00:01 remaining)
Stats: 0:00:26 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE Timing: About 76.19% done; ETC: 17:37 (0:00:01 remaining)
Nmap scan report for jerry.htb (10.129.136.9)
Host is up (0.24s latency).
Not shown: 999 filtered tcp ports (no-response)
PORT     STATE SERVICE
8080/tcp open  http-proxy
| http-slowloris-check: 
|   VULNERABLE:
|   Slowloris DOS attack
|     State: LIKELY VULNERABLE
|     IDs:  CVE:CVE-2007-6750
|       Slowloris tries to keep many connections to the target web server open and hold
|       them open as long as possible.  It accomplishes this by opening connections to
|       the target web server and sending a partial request. By doing so, it starves
|       the http server's resources causing Denial Of Service.
|       
|     Disclosure date: 2009-09-17
|     References:
|       http://ha.ckers.org/slowloris/
|_      https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2007-6750
| http-enum: 
|   /examples/: Sample scripts
|   /manager/html/upload: Apache Tomcat (401 Unauthorized)
|_  /manager/html: Apache Tomcat (401 Unauthorized)


```

* **Directory Enumeration (if applicable):**
    * Tools used (Gobuster, Dirbuster, etc.)
    * Interesting directories or files discovered.
* **Other Recon Tools (if applicable):**
    * Mention any additional recon techniques or tools used.

## Vulnerability Identification

* **Vulnerability 1:**
    * Describe the identified vulnerability in detail.
    * ![Screenshot or PoC]
    * Explain the exploitation process.
* **Vulnerability 2 (if any):**
    * Repeat the same format for any additional vulnerabilities.

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