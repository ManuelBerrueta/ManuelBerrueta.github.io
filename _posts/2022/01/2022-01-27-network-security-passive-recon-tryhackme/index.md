---
title: "Network Security - Passive Recon [TryHackMe]"
date: "2022-01-27"
tags: 
  - "ctf"
  - "cybersecurity"
  - "dig"
  - "dns"
  - "dnsdumpster"
  - "footprinting"
  - "hacking"
  - "infosec"
  - "network"
  - "nslookup"
  - "offensivesec"
  - "oscp"
  - "passiverecon"
  - "pentesting"
  - "recon"
  - "reconnaissance"
  - "security"
  - "shodan"
  - "tryhackme"
  - "whois"
---

When we engage in passive recon we are looking at information that is publicly available without interacting directly with the target. Here I summarize some of the tooling and help answer the questions.

## whois

`whois` is a tool that uses the query and response protocol which "searches for an object in an RFC 3912 database". With it we can gather information such as contact information (this can often be "incorrect" when a privacy service is used), registrar, registration date, registration updates and registration expiration dates, contact emails, among others.  
   
**Example**:

```sh
whois tryhackme.com
   Domain Name: TRYHACKME.COM
   Registry Domain ID: 2282723194_DOMAIN_COM-VRSN
   Registrar WHOIS Server: whois.namecheap.com
   Registrar URL: http://www.namecheap.com
   Updated Date: 2021-05-01T19:43:23Z
   Creation Date: 2018-07-05T19:46:15Z
   Registry Expiry Date: 2027-07-05T19:46:15Z
   Registrar: NameCheap, Inc.
   Registrar IANA ID: 1068
   Registrar Abuse Contact Email: abuse@namecheap.com
   Registrar Abuse Contact Phone: +1.6613102107
   Domain Status: clientTransferProhibited https://icann.org/epp#clientTransferProhibited
   Name Server: KIP.NS.CLOUDFLARE.COM
   Name Server: UMA.NS.CLOUDFLARE.COM
   DNSSEC: unsigned
   URL of the ICANN Whois Inaccuracy Complaint Form: https://www.icann.org/wicf/
>>> Last update of whois database: 2021-11-02T07:40:12Z <<<
<<<---------------------Snipped--------------------->>>
```

**Q & A**:  
When was TryHackMe.com registered? **20180705**  
What is the registrar of TryHackMe.com? **namecheap.com**  
Which company is TryHackMe.com using for name servers? **cloudflare.com**

## nslookup

We can use `nslookup` (Name Server LookUp) to query Domain Name System (DNS) servers to map a domain name to an IP as well as other DNS records. It can used interactively and non-interactively. For our purposes we will use it non-interactively and pass arguments.  
  Here is the basic synopsis from the man page: `nslookup [-option] [name | -] [server]`  
&nsbp;  
Using the `-type` flag, we can specify the query type, query types are case insensitive.

**Examples of different queries**:

```sh
# IPv4 via Cloudflare
nslookup -type=A tryhackme.com 1.1.1.1 

# Email servers
nslookup -type=MX tryhackme.com

# Other query types:
AAA - IPv6
CNAME - Cano
```

It is useful to know that you may be able to find targets that may be within scope using this tool, such as email servers that have not been patched in which you might be able to leverage a vulnerability against to gain a foothold.  
**Q & A**:  
Check the TXT records of thmlabs.com. What is the flag there?

```sh
nslookup -type=txt thmlabs.com
Server:         172.31.208.1
Address:        172.31.208.1#53

Non-authoritative answer:
thmlabs.com     text = "THM{a-------------8}"
```

## dig

`dig` (Domain Information Groper) is an additional tool for querying DNS.  
   
Synopsis from that man page:

```sh
dig [@server] [-b address] [-c class] [-f filename] [-k filename] [-m] [-p port#] [-q name] [-t type] [-v]
           [-x addr] [-y [hmac:]name:key] [[-4] | [-6]] [name] [type] [class] [queryopt...]
```

**Example**:

```sh
dig tryhackme.com MX

; <<>> DiG 9.16.1-Ubuntu <<>> tryhackme.com MX
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 62043
;; flags: qr rd ad; QUERY: 1, ANSWER: 12, AUTHORITY: 0, ADDITIONAL: 0
;; WARNING: recursion requested but not available

;; QUESTION SECTION:
;tryhackme.com.                 IN      MX

;; ANSWER SECTION:
tryhackme.com.          0       IN      MX      5 alt1.aspmx.l.google.com.
tryhackme.com.          0       IN      MX      1 aspmx.l.google.com.
<<<---------------------Snipped--------------------->>>
```

## DNSDumpster

While we can gather a lot of useful information from the tools above, they are not designed for enumeration of subdomains. Subdomains can have useful information and servers that are not patched regularly and that may be vulnerable.    
We can use the [DNSDumpster.com](dnsdumpster.com) service to discover subdomains, their IP addresses and attempt to geolocate them. It is simple to use, we just give it our target domain and it will do all the work for us :).  
**Q & A**:  
Lookup tryhackme.com on DNSDumpster. What is one interesting subdomain that you would discover in addition to www and blog? **r\*\*\*\*e**

## Shodan

We can use [Shodan.io](https://www.shodan.io) to learn more about our target's network. We can think of Shodan like a search engine for devices online, and since you are not directly connecting to the devices, it compliments your passive research very well!  
**Q & A**:  
According to Shodan.io, what is the 2nd country in the world in terms of the number of publicly accessible Apache servers?  
Let's try searching for `Apache`, we may find a country that looks something like this **G**\***y**.  
   
Based on Shodan.io, what is the 3rd most common port used for Apache? **8--0**  
   
Based on Shodan.io, what is the 3rd most common port used for nginx?  
Let's try searching for `nginx`, we get something like **8--8**
