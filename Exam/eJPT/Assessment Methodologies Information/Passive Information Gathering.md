**What is Passive Info gathering** : To gather information without actively interacting with the target.

#### command
- `host`: to get IP information

```bash
xxxxx@xxxx-MacBook-Air ~ % host hackersploit.org

hackersploit.org has address 104.21.44.180

hackersploit.org has address 172.67.202.99

hackersploit.org has IPv6 address 2606:4700:3036::ac43:ca63

hackersploit.org has IPv6 address 2606:4700:3031::6815:2cb4

hackersploit.org mail is handled by 0 _dc-mx.2c2a3526b376.hackersploit.org.
```

- `whatweb` command :  a default command on kali / parrot for getting more insights on the target
	- Details : https://www.kali.org/tools/whatweb/

```bash
root@kali:~# whatweb -v -a 3 192.168.0.102
WhatWeb report for http://192.168.0.102
Status    : 200 OK
Title     : Toolz TestBed
IP        : 192.168.0.102
Country   : RESERVED, ZZ

Summary   : JQuery, Script, X-UA-Compatible[IE=edge], HTML5, Apache[2.2,2.2.22], HTTPServer[Ubuntu Linux][Apache/2.2.22 (Ubuntu)]

Detected Plugins:
[ Apache ]
  The Apache HTTP Server Project is an effort to develop and
  maintain an open-source HTTP server for modern operating
  systems including UNIX and Windows NT. The goal of this
  project is to provide a secure, efficient and extensible
  server that provides HTTP services in sync with the current
  HTTP standards.

  Version      : 2.2.22 (from HTTP Server Header)
  Version      : 2.2
  Version      : 2.2
  Google Dorks: (3)
  Website     : http://httpd.apache.org/

[ HTML5 ]
  HTML version 5, detected by the doctype declaration


[ HTTPServer ]
  HTTP server header string. This plugin also attempts to
  identify the operating system from the server header.

  OS           : Ubuntu Linux
  String       : Apache/2.2.22 (Ubuntu) (from server string)

[ JQuery ]
  A fast, concise, JavaScript that simplifies how to traverse
  HTML documents, handle events, perform animations, and add
  AJAX.

  Website     : http://jquery.com/

[ Script ]
  This plugin detects instances of script HTML elements and
  returns the script language/type.


[ X-UA-Compatible ]
  This plugin retrieves the X-UA-Compatible value from the
  HTTP header and meta http-equiv tag. - More Info:
  http://msdn.microsoft.com/en-us/library/cc817574.aspx

  String       : IE=edge

HTTP Headers:
  HTTP/1.1 200 OK
  Server: Apache/2.2.22 (Ubuntu)
  Last-Modified: Fri, 02 Feb 2018 15:27:56 GMT
  ETag: "11f-2e38-5643c5b56a8d3"
  Accept-Ranges: bytes
  Vary: Accept-Encoding
  Content-Encoding: gzip
  Content-Length: 3541
  Connection: close
  Content-Type: text/html

root@kali:~#
```

