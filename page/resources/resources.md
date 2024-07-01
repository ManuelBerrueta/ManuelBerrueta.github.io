---
title: "Resources"
date: "2019-07-07"
---
   
# Where to start…
Getting started of security whether it be penetration testing, DFIR, reverse engineering, etc can be a little overwhelming. The good news is that there is a lot of resources out there and the community is very helpful. Depending on what you are trying to learn, there is some resources below to help you get started. I would recommend trying to stick to one thing at first and once you get some experience and gain some confidence then go ahead and branch out to other things.

In the beginning it might be useful to play around with a few different concepts and tools and once you find something that really grasps your interest, dig deeper into and try to master it. YouTube is your friend, the community shares a lot of stuff and there is a lot of good tutorials and information.

## Things I recommend before you begin…
I highly recommend that you get started with some basic knowledge of networks and learn how to do some programming, especially in a scripting language. I highly recommend Python, because it is highly supported and a very powerful tool that you can use to write scripts as well as a full application.

For reverse engineering I recommend you learn assembly before you attempt to reverse any software/malware. You can start with something simple like MIPS and do some assembly coding so you can get an idea of how it works. Writing things in C and then disassembling the code to see what it looks like in assembly is greatly beneficial, this can help you learn some of the C constructs.
    
    
---     
    
# Books & Reading
## Software Engineering
- Clean Code
- The pragmatic programmer
- Code Complete
- The Mythical Man-Month
- [The Security Development Lifecycle](https://blogs.msdn.microsoft.com/microsoft_press/2016/04/19/free-ebook-the-security-development-lifecycle/) \[free ebook\]
- Threat Modeling: Designing for Security

## Reverse Engineering / DFIR
- Practical Malware Analysis
    - Must have in my opinion (and many others =) ) to get started in reverse engineering & malware analysis. If you can only get one book, this would be it for me.
- The IDA Pro Book
    - Good reference guide for IDA Pro
- Practical Reverse Engineering
- Attacking Network Protocols
- Windows Internals Part 1
    - Get a better understanding of Windows
- Practical Binary Analysis
- The Art of Memory Forensics
- Practical Forensic Imaging: Securing Digital Evidence with Linux Tools
- [https://beginners.re/](https://beginners.re/)

## Exploit Dev
- The Shellcoder’s Handbook: Discovering and Exploiting Security Hole

## Offensive Security: Penetration Testing, Red Teaming, AppSec
- Rtfm: Red Team Field Manual
- Gray Hat Hacking
- The Hacker Playbook series
- Penetration Testing
- The Web Application Hacker’s Handbook
- Attacking Network Protocols
- Windows Internals Part 1
    - Get a better understanding of Windows
- Troubleshooting with the Windows Sysinternals Tools
    - Reference on how to get the most out of the Sysinternals suite

## Cryptography
- Serious Cryptography: A Practical Introduction to Modern Encryption  
    (I suggest starting with this one)
- Applied Cryptography: Protocols, Algorithms and Source Code in C
- Cryptography Engineering: Design Principles and Practical Applications
[https://pagedout.institute/](https://pagedout.institute/)

---     
# Tools
## Software Engineering
- VIM =)
- Visual Studio Code
    - Extensions
        - Better Comments
        - Shell Launcher
            - Allows you to switch Shells (Super useful)
        - Bracket Pair Colorizer (very useful)
        - Indenticator
        - indent-rainbow
        - Bookmarks
        - Todo Tree
    - Language Extensions
        - C/C++ – Microsoft
        - C# – Microsoft
        - PowerShell – Microsoft
        - Python – Microsoft
        - x86 and x64 Assembly
    - Themes
        - Noctis
        - Material
        - Monokai Pro
        - Cobalt2
        - Dracula
        - Night Owl
        - Rainglow
- Visual Studio IDE
- Atom
- Sublime Text

## General
- [VMWare Workstation Pro](https://www.vmware.com/products/workstation-pro/workstation-pro-evaluation.html)
    - Virtual Machine
    - Alternatively get [VMWare Workstation Player](https://www.vmware.com/products/workstation-player.html) which is free, but doesn’t have the ability to take snapshots.
- [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
    - Virtual Machine
    - FREE
    - Snapshots!
- WSL – Windows Subsystem for Linux
    - Super useful when doing command line stuff on windows without having to fire up a VM
- PowerShell
    - Learn to use it, very powerful
- Linux
    - Choose your flavor and learn to use it well
- Cygwin
- [VirusTotal](https://www.virustotal.com/)
    - Not too sure if that file you downloaded is safe? Upload it to VirusTotal!
    
## Python Useful Modules
- Scapy
- RE
- Socket
- Scrapy
Reverse Engineering
- [IDA Pro](https://www.hex-rays.com/products/ida/)
- [Binary Ninja](https://binary.ninja/)
    - Plugins
        - [Bookmarks](https://github.com/joshwatson/binaryninja-bookmarks/tree/83f620688b5f652a29386d26f3007202a110d075)
        - [SyscallIdentify](https://github.com/Neolex-Security/SyscallsIdentifier_BinaryNinja/tree/7d6bb50387187a96f0d8291b0567e38e7213f65d)
- [Ghidra](https://ghidra-sre.org/)
- [ODA (Online Disassembler)](https://onlinedisassembler.com/static/home/index.html)
- Hopper
- Radare
- [Frida – Dissasembler](https://www.frida.xyz/)
- Binwalk
- Snowman
- JD-Gui
- x64Dbg
- WinDbg
- Wireshark
- Sysinternals
- REMnux
- FLARE VM
- [Frida.re – Dynamic Instrumentation Toolkit](https://www.frida.re/)
- .Net Stuff
    - dnSpy
    - dotPeek
    - dotMemory

## DFIR
- Volatility
- SIFT Workstation
Penetration Testing
- Metasploit
- Nmap
- Nessus
- Hashcat

## Web Penetration Testing
- [Burp Suite](https://portswigger.net/)
- Firefox plugins:
    - FoxyProxy
    - Wappalyzer or BuiltWith
- DirBuster
    - Enumerates directories/paths/subpaths in a given domain
- [Sublist3r](https://github.com/aboul3la/Sublist3r)
    - Uses OSINT from search engines to help enumerate subdomains
- [Knockpy](https://github.com/santiko/KnockPy)
    - Enumerate subdomains using a provided word list
- [Striker](https://github.com/s0md3v/Striker)
    - Cloudflare bypass
    - DNS Enum
    - Checks for WordPress use
    - And more
## Fuzzing
- [AFL](http://lcamtuf.coredump.cx/afl/)
- [BooFuzz](https://github.com/jtpereyda/boofuzz)
- [Wfuzz](https://github.com/xmendez/wfuzz)

## Security Tools for All
- [CyberChef](https://gchq.github.io/CyberChef/)
- [SecLists](https://github.com/danielmiessler/SecLists)

## Hardware Tools
- [https://null-byte.wonderhowto.com/how-to/run-usb-rubber-ducky-scripts-super-inexpensive-digispark-board-0198484/](https://null-byte.wonderhowto.com/how-to/run-usb-rubber-ducky-scripts-super-inexpensive-digispark-board-0198484/)
- [https://www.aliexpress.com/item/4000051256454.html](https://www.aliexpress.com/item/4000051256454.html)

## **Reference**
- Reverse Engineering
    - [x86 Assembly Instruction Reference](https://kernfunny.org/x86/)
- Known bad hosts/ C2Cs
    - [https://www.malwaredomainlist.com/mdl.php](https://www.malwaredomainlist.com/mdl2.php?inactive=&sort=Date&search=&colsearch=All&ascordesc=DESC&quantity=100&page=0)

---    
# Blogs / Websites
## Mix
- [https://0xdarkvortex.dev/](https://0xdarkvortex.dev/)
- [https://0ffset.net/](https://0ffset.net/)
- [https://msrc-blog.microsoft.com/category/srd/](https://msrc-blog.microsoft.com/category/srd/)
- [https://threatvector.cylance.com/](https://threatvector.cylance.com/)
- [https://www.fireeye.com/blog.html](https://www.fireeye.com/blog.html)
- [https://googleprojectzero.blogspot.com/](https://googleprojectzero.blogspot.com/)
- [https://vxug.fakedoma.in/](https://vxug.fakedoma.in/)

## Reverse Engineering
- [https://medium.com/@ryancor/reverse-engineering-encrypted-code-segments-b01aead67701](https://medium.com/@ryancor/reverse-engineering-encrypted-code-segments-b01aead67701)
- [https://www.malwaretech.com/](https://www.malwaretech.com/)
- [https://malwareunicorn.org/](https://malwareunicorn.org/#/)
- [https://about.me/hasherezade](https://about.me/hasherezade)
- [https://mayahustle.com](https://mayahustle.com/)/

### Microsoft / Windows
- [http://www.geoffchappell.com/](http://www.geoffchappell.com/)
- [https://tyranidslair.blogspot.com/](https://tyranidslair.blogspot.com/)
- [https://sandboxescaper.blogspot.com/2019/10/hunting-for-filesystem-bugs.html](https://sandboxescaper.blogspot.com/2019/10/hunting-for-filesystem-bugs.html)
- [https://www.cyberark.com/threat-research-blog/follow-the-link-exploiting-symbolic-links-with-ease/](https://www.cyberark.com/threat-research-blog/follow-the-link-exploiting-symbolic-links-with-ease/)
- [https://windows-internals.com/category/windows-internals/](https://windows-internals.com/category/windows-internals/)
- [https://youtu.be/0KO3oGXtMNo](https://youtu.be/0KO3oGXtMNo)
  
## DFIR
- [https://aboutdfir.com/](https://aboutdfir.com/)

---     
# Podcasts
- Risky Business
- Security Now
- Security Weekly
- Darknet Diaries
- BHIS Podcast
- TrustedSec Podcast

---    
# Conferences
- DEFCON
- Black Hat
- BSides
- S4
- INFILTRATE

---   
# Training
- [SANS Institute](https://www.sans.org/)
- [OSCP](https://www.offensive-security.com/information-security-certifications/oscp-offensive-security-certified-professional/)
- [Cybrary](https://www.cybrary.it/)
- [Pluralsight](https://www.pluralsight.com/)
- [ISC2](https://www.isc2.org/)
- [Cyber Aces](https://tutorials.cyberaces.org/)
- [Infosec Institute](https://www.infosecinstitute.com/)
- [SecureNinja](https://secureninja.com/)
- [YouTube](https://www.youtube.com/)
- [OpenSecurityTraining](http://opensecuritytraining.info/)
- [EC-Council](https://www.eccouncil.org/)
- [NICCS](https://niccs.us-cert.gov/training)
- [ICS-CERT](https://ics-cert-training.inl.gov/learn)
- [VeteranSec.com](https://veteransec.com/)
    - [https://github.com/VetSec/awesome-infosec](https://github.com/VetSec/awesome-infosec)
- [https://workplus.splunk.com/veterans](https://workplus.splunk.com/veterans)
- [withyouwithme.com](http://withyouwithme.com)
- [https://www.bleepingcomputer.com/news/security/free-cybersecurity-training-now-available-for-us-veterans/](https://www.bleepingcomputer.com/news/security/free-cybersecurity-training-now-available-for-us-veterans/)
- [https://samsclass.info/](https://samsclass.info/)
- University/College
    - Computer Science Degree
    - Software Engineering Degree
    - Cyber Security Degree

## ICS
- [https://www.smartgrid.gov/files/National\_SCADA\_Test\_Bed\_Handson\_Control\_System\_Cyber\_Securit\_200911.pdf](https://www.smartgrid.gov/files/National_SCADA_Test_Bed_Handson_Control_System_Cyber_Securit_200911.pdf)
- [https://github.com/Ka0sKl0wN/ICS-Security-Study-Resources](https://github.com/Ka0sKl0wN/ICS-Security-Study-Resources)

---    
## Certifications Prep
### GIAC
#### Building an Index:
- [https://tisiphone.net/2015/08/18/giac-testing/](https://tisiphone.net/2015/08/18/giac-testing/)
- [https://www.ericooi.com/how-to-build-a-sans-giac-index/](https://www.ericooi.com/how-to-build-a-sans-giac-index/)
- [https://www.youtube.com/watch?v=bHpkTArlXWc&feature=emb\_logo](https://www.youtube.com/watch?v=bHpkTArlXWc&feature=emb_logo)

## Hacking, Code & Coffee
- [https://h0mbre.github.io/Learn-C-By-Creating-A-Rootkit/#](https://h0mbre.github.io/Learn-C-By-Creating-A-Rootkit/#)
- [https://vxug.fakedoma.in/papers/FO/1/Masking%20Malicious%20Memory%20Artifacts%20%E2%80%93%20Part%20I%3A%20Phantom%20DLL%20Hollowing.html](https://vxug.fakedoma.in/papers/FO/1/Masking%20Malicious%20Memory%20Artifacts%20%E2%80%93%20Part%20I%3A%20Phantom%20DLL%20Hollowing.html)

---    
# Terminology
## Network
**Hub**: All incoming traffic is replicated out onto all ports (Layer 1 - Physical)  
**Bridge**: Connects 2 physical segments together (Layer 2 - Data Link)  
**Switch**: LAN - physical connection of network segments. Sends traffic based on MAC Address (Layer 3 - Network)  
**Router**: Connects networks together and determines route to take. Send traffic based on IP addresses. (Layer 3 - Network)  
**Subnet Mask**: Specifies which part of a given IP address is the network address and which part is the computer on that network's.  
i.e. given Subnet Mask: 255.255.0.0 and IP address of 192.168.1.4  
In the Subnet Mask the non zero represents the parts of the IP that belong to the network, in this case it will be 192.168 the .1.4 is the machine/device in that network.  
**PAN**: Personal Area Network. Bluetooth, Zigbee, NFC and RFID all fall under the PAN domain.  
**Zigbee**: Low power, low bandwidth PAN technology. Leverages use of mesh networking due to its limited range. Uses in light bulbs, locks, HVAC, medical devices, etc..  
**NFC**: Near Field Communications PAN technology. Mean to be a closed proximity technology with a limited range of 1-2 inches. Subset of RFID. Uses are contact less smart phone payments, authentication(badges), credit card payments, transit passes. Security is not mandated.  
**RFID**: Radio Frequency Identification: Use to identify objects and tracking location, PAN technology. Typically used with an RFID tag and and RFID reader. RFID tags can be significantly small. Can operate at great distance.  
**5G**: Digital cellular network with speeds of 1-2 gigabit. Features low latency, high bandwidth and high density support.