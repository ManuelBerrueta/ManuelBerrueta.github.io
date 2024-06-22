---
title: "Notes"
date: "2019-07-08"
---

## **Linux**

#### Commands

- `locate` fileName
- `which` fileName
- `xxd` fileName
- curl -k URL.com  
    [https://www.cyberciti.biz/faq/how-to-curl-ignore-ssl-certificate-warnings-command-option/](https://www.cyberciti.biz/faq/how-to-curl-ignore-ssl-certificate-warnings-command-option/)
- wget -r -nH --cut-dirs=10 --no-parent --reject="index.html\*" https://my.target\_url.com/  
    #download all files from that location ignoring the directory structure of the web target and without index.htmls. --cut-dirs=10 gets rid of the folder structure from t he target.
- grep: -A5 = Display 5 ln after match | -B5 Display 5 ln before match  
    \-nri //NumRecursiveIgnorecase
- cut -f2 -d ":" //filters the content piped to cut to the 2nd field with a delimiter on ":"
- dmesg
- [https://explainshell.com/](https://explainshell.com/)

Terminal Tricks

- CTRL + U : Cancel current input
- CTRL + R : reverse search
- CTRL + A : Move to beginning of line
- sudo !! : Run previous command with sudo
- CTRL + Z & fg: Move task to background, and bring it back
- [https://www.howtogeek.com/howto/ubuntu/keyboard-shortcuts-for-bash-command-shell-for-ubuntu-debian-suse-redhat-linux-etc/](https://www.howtogeek.com/howto/ubuntu/keyboard-shortcuts-for-bash-command-shell-for-ubuntu-debian-suse-redhat-linux-etc/)

#### Server

- echo "Test" | write user.name #Send Test to user "user.name"
- echo "Test" | wall #Send a message to the whole server

#### Network

- [ifconfig vs ip command](https://www.tecmint.com/ifconfig-vs-ip-command-comparing-network-configuration/)
- List of ports + Known Malicious Use: [SpeedGuide Port Databas](https://www.speedguide.net/ports.php)[e](https://www.speedguide.net/ports.php)

#### Script referencing

- [Bash for loop](https://linuxize.com/post/bash-for-loop/)

#### Tmux

- [https://github.com/tmux/tmux/wiki/FAQ](https://github.com/tmux/tmux/wiki/FAQ)
- [https://tmuxcheatsheet.com/](https://tmuxcheatsheet.com/)

#### Issues building VMware Workstation on Parrot OS or other Linux distro?  
Use the script found on this page:  
[https://communities.vmware.com/thread/609330](https://communities.vmware.com/thread/609330)

Need to compile/target 32 bit in 64 bit linux distro?  
sudo apt-get install gcc-multilib

## Windows

- SHIFT+Right Click = Access to Open PowerShell and Linux Shell Here
- Hosts file location: C:\\Windows\\System32\\drivers\\etc\\

Hyper-V

CTRL + ALT + LEFT to release lock on Hyper-V screen :)

[https://docs.microsoft.com/en-us/windows-server/virtualization/hyper-v/learn-more/hyper-v-virtual-machine-connect#:~:text=Press%20CTRL%2BALT%2BLEFT%20arrow,settings%20in%20Hyper%2DV%20Manager.&text=Select%20Action%20%3E%20Ctrl%2BAlt%2B,combination%20CTRL%2BALT%2BEND.](https://docs.microsoft.com/en-us/windows-server/virtualization/hyper-v/learn-more/hyper-v-virtual-machine-connect#:~:text=Press%20CTRL%2BALT%2BLEFT%20arrow,settings%20in%20Hyper%2DV%20Manager.&text=Select%20Action%20%3E%20Ctrl%2BAlt%2B,combination%20CTRL%2BALT%2BEND.)

- Get Hyper-V VMs IP Address: get-vm | ?{$\_.State -eq "Running"} | select -ExpandProperty networkadapters | select vmname, macaddress, switchname, ipaddresses | ft -wrap -autosize

## PowerShell

- whoami /all | whoami /all /fo list == Show SAT  
    whoami /priv == Show privileges  
    Get-Acl <resource> | Format-List == Show resource's ACL  
    Get-SmbShare or \\\\localhost\\HidShare$ or \\\\ <machine>\\HidShare$  
    net localgroup administrators  
    net use \\\\<ip>\\[ipc$](https://support.microsoft.com/en-us/help/3034016/ipc-share-and-null-session-behavior-in-windows) "" /user:"" Null Session Interprocess comm share  
    NBTSTAT -A <target.ip> //NetBIOS  
    dir env:\\ or in cmd use "set" // View Env Vars  
    findstr  
    help <commandName>  
    wmic process list full

#### Network

- netstat -naob == list ports, pid, executable name

### References

- [https://docs.microsoft.com/en-us/windows/win32/apiindex/windows-api-list](https://docs.microsoft.com/en-us/windows/win32/apiindex/windows-api-list)

## Google

Use **`site:`** followed by your search word. This will only search in that site for the given target word or string.

**`filtetype:`**pdf myStrForFileName to find files

Use quotes **`“ “`** to have your search find the string in the same order

Use `*` as a wildcard for a word  

References: [https://support.google.com/websearch/answer/2466433?hl=en&visit\_id=1-636359231656004331-872652267&rd=1](https://support.google.com/websearch/answer/2466433?hl=en&visit_id=1-636359231656004331-872652267&rd=1)

## Python

[https://gto76.github.io/python-cheatsheet/](https://gto76.github.io/python-cheatsheet/)

- Loops
    - [https://treyhunner.com/2016/04/how-to-loop-with-indexes-in-python/](https://treyhunner.com/2016/04/how-to-loop-with-indexes-in-python/)

## x64dbg

- [https://reverseengineeringtips.blogspot.com/2015/01/an-introduction-to-x64dbg.html](https://reverseengineeringtips.blogspot.com/2015/01/an-introduction-to-x64dbg.html)

## File Headers

[https://en.wikipedia.org/wiki/List\_of\_file\_signatures](https://en.wikipedia.org/wiki/List_of_file_signatures)

## Email

Email Parser: [https://mha.azurewebsites.net/](https://mha.azurewebsites.net/)  
[https://testconnectivity.microsoft.com/](https://testconnectivity.microsoft.com/)

## String & Data Conversions

[https://www.asciitohex.com/](https://www.asciitohex.com/)  
[https://ss64.com/convert.html](https://ss64.com/convert.html)

## Networking

- TCPDump
    - Use -n switch to display IP instead of using name resolution(can be modified and misleading)
    - \-X to show Hex/Ascii of the packet
    - When looking at a packet flags, the . == ack.  
        i.e. Flags \[S.\] == SYN/ACK or Flags \[ . \] == ACK
- [https://www.sans.org/security-resources/tcpip.pdf](https://www.sans.org/security-resources/tcpip.pdf)

## Networking References

- [Intro to Networking Terminology](https://www.digitalocean.com/community/tutorials/an-introduction-to-networking-terminology-interfaces-and-protocols)
- [List of Common Ports](https://www.utilizewindows.com/list-of-common-network-port-numbers/)
- [List\_of\_network\_protocols per\_OSI\_model Layer](https://en.wikipedia.org/wiki/List_of_network_protocols_\(OSI_model\))
- [Network\_packet](https://en.wikipedia.org/wiki/Network_packet)
- [ICMP Types & Codes](https://www.ibm.com/support/knowledgecenter/en/SS42VS_7.3.2/com.ibm.qradar.doc/c_DefAppCfg_guide_ICMP_intro.html)

## SSH

- ssh-keygen = Generate new private & public key pair
- If you already have a key pair:
    - Copy your current "id\_rsa" and "id\_rsa.pub" to "~/.ssh/" directory
    - Run the command: "chmod 400 ~/.ssh/id\_rsa" to tighten access
    - Run "ssh-add ~/.ssh/id\_rsa" to add the keys to the auth agent

## VSCode

#### Building and debugging C/C++ source in Linux

- Create a new file with a ".c" extension
- With that C file open:
    - Go to Terminal > Run Build Task... OR ctrl+shift+b
    - gcc should pop up, select the little gear/settings in the corner
        - This will make a tasks.json in the .vscode folder
    - Go to Debug > Start Debugging
        - This should make a launch.json
- Now you should be able to hit 5 and debug as you please =)

## Crypto

Nice article on how SSL/TLS works from digicert [here](https://www.digicert.com/ssl-cryptography.htm)

#### Encrypting

[https://www.digitalocean.com/community/tutorials/how-to-use-gpg-to-encrypt-and-sign-messages](https://www.digitalocean.com/community/tutorials/how-to-use-gpg-to-encrypt-and-sign-messages)  
[https://yanhan.github.io/posts/2017-09-27-how-to-use-gpg-to-encrypt-stuff.html](https://yanhan.github.io/posts/2017-09-27-how-to-use-gpg-to-encrypt-stuff.html)  
[https://www.gnupg.org/gph/en/manual/x110.html](https://www.gnupg.org/gph/en/manual/x110.html)

## Cheat Sheets

- [https://pen-testing.sans.org/resources/downloads](https://pen-testing.sans.org/resources/downloads)
- [http://blog.commandlinekungfu.com/](http://blog.commandlinekungfu.com/)

# Testing

## Scanning

nmap 192.168.1.0/24 | 192.168.1.\* | 192.168.1.0-24  
\-sV : version detection; -PN : no ping ; -sS Stealth ; -sT 3Way OPEN?  
\-sA send ACKs-sU : UDP scan ; -sn host discovery only; no port scan  
\-p#s: only scan port#s; --top-ports # : scan only # top ports  
\-v : verbose output; -T 0-5 scan speed 0=slowest 5=fastest  
\-oN | -oX | -oG <file>: output to file in Normal, XML or grepable.  
\-n Do not DNS name resolution (faster)  
ndiff priorScan newScan : shows diff between 2 scans

- [https://hackertarget.com/nmap-cheatsheet-a-quick-reference-guide/](https://hackertarget.com/nmap-cheatsheet-a-quick-reference-guide/)

arp-scan \[Very Noisy\]  
netdiscover -p \[-p = passive, low key!\]

### Wireshark

[https://wiki.wireshark.org/](https://wiki.wireshark.org/)  
Statistics -> Conversations  
Statistics -> I/O Graphs
