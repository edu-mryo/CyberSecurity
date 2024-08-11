## DNS Zone Transfer
- DNS records are instructions stored within DNS servers. They provide various types of information about a domain. Let's look at some common record types using `google.com` as an example:

- **A (Address) Record:** This record points a domain name to an IPv4 address.
    
    - Example: `google.com A 142.250.181.14`
    - This means that when you type `google.com` into your browser, your computer will look up this A record and connect to the IP address 142.250.181.14 to reach Google's servers.

- **AAAA (IPv6 Address) Record:** Similar to A records, but for IPv6 addresses. IPv6 is the newer, more expansive addressing system for the internet.
    
    - Example: `google.com AAAA 2404:6800:4003:c02::64`

- **NS (Name Server) Record:** Specifies which authoritative name servers are responsible for a domain. These servers hold the actual DNS records for the domain.
    
    - Example: `google.com NS ns1.google.com`
    - This indicates that `ns1.google.com` is one of the name servers that holds the DNS records for `google.com`.
    - From this NS, you can resolve IP

- **MX (Mail Exchange) Record:** Identifies the mail servers responsible for handling email for a domain.
    
    - Example: `google.com MX 10 aspmx.l.google.com.`
    - This tells email systems where to send mail addressed to `@google.com`. The number '10' indicates priority; lower numbers have higher priority.

- **CNAME (Canonical Name) Record:** Creates an alias for one domain name to point to another.
    
    - Example: `www.google.com CNAME google.com.`
    - This means that `www.google.com` is an alias for `google.com`. Any requests for `www.google.com` will be directed to `google.com`.

- **TXT (Text) Record:** Stores arbitrary text information. Often used for email authentication (SPF) and domain ownership verification.
    
    - Example: `google.com TXT "v=spf1 include:_spf.google.com ~all"`

- **HINFO (Host Information) Record:** Provides information about the hardware and operating system of a host. Rarely used these days.
    
    - Example: `host.example.com HINFO "PC-Intel" "Linux"` (hypothetical example, not likely for Google)

- **SOA (Start of Authority) Record:** Essential record for every zone. Specifies the primary name server, contact email, and various zone parameters.
    
    - Example: `google.com SOA ns1.google.com. dns-admin.google.com. 2023080901 7200 3600 1209600 300`

- **SRV (Service) Record:** Defines the location (hostname and port number) of specific services, like VoIP or instant messaging.
    
    - Example: `_sip._tcp.example.com SRV 0 5 5060 sipserver.example.com.` (hypothetical example, not likely for Google)

- **PTR (Pointer) Record:** The reverse of an A record. Maps an IP address back to a domain name. Used for reverse DNS lookups.
    
    - Example: `14.181.250.142.in-addr.arpa PTR google.com.` (hypothetical example)

- Most common DNS server
	- `1.1.1.1`: Cloudflare
	- `8.8.8.8`: Google

### Zonetransfer
- **Zonetransfer**, also sometimes referred to as **AXFR** (a DNS query type), is a mechanism used in the Domain Name System (DNS) to replicate and synchronize all DNS records for a particular zone between DNS servers.   

- **In simpler terms**: It's like making a copy of the entire DNS "phonebook" for a specific domain (the zone) and transferring that copy to another DNS server.   

How it works:

1. **Primary and Secondary Servers**: Typically, there's a primary DNS server that holds the authoritative copy of the zone's records. Secondary servers request a zone transfer from the primary to keep their copies up-to-date.   
2. **Initiation**: The secondary server initiates the zone transfer by sending a special request to the primary.   
3. **Transfer**: If allowed, the primary server sends all its DNS records for the zone to the secondary server.
4. **Synchronization**: The secondary server updates its own database with the received records, ensuring it has the latest information.

### `dnsenum` command
- `dnsenum`: [Link](https://www.kali.org/tools/dnsenum/)
	- A multithreaded perl script to enumerate DNS information of a domain and to discover non-contiguous ip blocks. 
	- The main purpose of Dnsenum is to gather as much information as possible about a domain. The program currently performs the following operations:

```txt
1) Get the host's addresse (A record).

2) Get the namservers (threaded).

3) Get the MX record (threaded).

4) Perform axfr queries on nameservers and get BIND VERSION (threaded).

5) Get extra names and subdomains via google scraping
   (google query = "allinurl: -www site:domain").

6) Brute force subdomains from file, can also perform recursion
   on subdomain that have NS records (all threaded).

7) Calculate C class domain network ranges and perform whois
   queries on them (threaded).

8) Perform reverse lookups on netranges
   ( C class or/and whois netranges) (threaded).

9) Write to domain_ips.txt file ip-blocks.
```


