---
title: "RE Craft"
date: "2021-01-22"
coverImage: "RE_Craft.jpg"
---

Below is my workflow, the tooling I use and how I approach reverse engineering. This is a refresh of an old post, that the great Roberto Rodriguez ([@Cyb3rWard0g](https://twitter.com/Cyb3rWard0g)) encouraged me to rewrite and update it. I will try to add to it and clean it up as I go, especially if I am encouraged to do so :)

## Before you start it would be useful to know:

- Assembly
    - Instructions
    - Registers
    - Code Constructs (loops, branching, etc..)
    - Basic Blocks (What they are)
    - Intel vs AT&T Syntax (I will stick with Intel)
        - Intel Example: mov eax, 0x01 ( mov is the instruction, and it moves 0x01 in to reg eax)
    - Big Endian / Little Endian
        - Big Endian is stored with MSB first
        - Little Endian is stored with LSB first
        - Example: Given the string 'APPLE' in hex 41 50 50 4c 45 In Big Endian it would be stored as we see it: 0x4150504c45 In Little Endian, the bytes are stored in reverse order: 0x454c505041
- Memory -Program layout in memory
- Programming (C/C++ would be especially useful)
- This is not easy, and you need a lot patience and perseverance!
- The more you do it, the better you are going to get!
- Take the time to learn the tools, it pays off!
- Research! Google is your friend!
- Linux terminal skills!
- Practice, Practice, Practice
    - If you don't use it, you loose it!
- Have a clear goal of what you are trying to do
    - This keeps you focus and you should strive to do only that, as little as possible to get to your goal!

I break my workflow down into 4 Steps: Initial Triage, Static Analysis, Dynamic Analysis, and Verification. At each of this steps you should be taking good notes! I am currently using OneNote since it syncs automatically and allows me to nest things in a meaningful way. Do not underestimate the power of good notes! I also use a combination of Linux and Windows tools, this is made a lot of easier and faster with WSL2. Another tool that may be worth taking a look at is called XMind, I seen it in a video by Jason Haddix, and I think it may work good for RE because it allows you to connect things togethers similar to a UML.

# VM

Depending on what I am working with, I will probably throw the file in question in to a VM. For virtualization software I have used VMware products, Oracle's VirtualBox (free!), Hyper-V, and QEMU. I used to mostly use Workstation Pro 16 but I personally feel like the performance has deteriorated on Windows. So, now I am using Hyper-V VMs on Windows and Workstation Pro 16 on Linux. I still use VirtualBox on occasion for some already spun up VMs that you can download for hacking fun! I also leverage the heck out of WSL2 and most of the times I can just work out of that.

## My VMs Setup

Before I put any funny files on any of my VMs I take a snapshot, this allows us to revert back to that previous state before we put the file into the VM. Which is extremely handy!

My current VM(s):

- Ubuntu (or Mint)
- Fedora
- Windows 10 VM

# Initial Triage

Here we are looking for low hanging fruit. This step is usually pretty quick enough and give you a lot of bang for your buck, per say. As you do it more often, it becomes pretty fast step and you may be able to get a lot of what you need (if you are very lucky, some times all you need!) and if not, it can at least give you some kind of direction.

## Tooling

```
- File
- Binwalk
- Strings + Floss
- Ldd
- Xxd
- Ldd
- Pedump
- Readelf
- Objdump
```

## Workflow

```bash
file <targetFile>  #Try to see what kind of file it is
```

```bash
binwalk <targetFile>  #similar output to file but generally gives me more useful information 
```

```bash
strings <targetFile>  #Prints to screen strings
Strings -n 3 <targetFile>  #Prints to screen strings that are at least 3 chars long
```

```bash
floss <targetFile> #Deobfuscates strings in the binary
```

```bash
xxd <targetFile> #Show hexdump of the binary
```

```bash
ldd <targetFile> #Show libraries/dependencies
```

```bash
pedump <targetFile> #display PE  information, includes Export table
```

```bash
readelf <targetFile> #display elf file information
```

```bash
objdump <targetFile> #display elf file information, similar to readelf
```

```PowerShell
Get-FileHash .\<targetFile> 
```

```bash
sha256sum <targetFile>  #Calculate SHA2 hash
md5sum <targetFile> #Calculate MD5 hash
```

```bash
strings <targetFile>  #Prints to screen strings
```

```bash
comm -3 <targetFile_1> <targetFile_2>  #Prints to screen difference between 2 files
```

With strings and floss we can get some possibly useful good amount of information. We can find interesting strings, IP address(es), URLs, hard coded passwords and keys (hopefully not…), and if you are working on a CTF challenge, you may just find a flag!

For doing diff on files, we can also use a tool called Beyond Compare (I found in one of [@h0mbre\_](https://twitter.com/h0mbre_)'s posts) to see exactly what bytes differ in a GUI.

We can use the calculated hashes to do a web search and see if anything comes up, we can also check in VirusTotal for possible matches and get more information, we can also check in the system to see if there is other copies of this file planted somewhere. I made a tool called [HashFinder](https://github.com/ManuelBerrueta/hashfinder), that can help with that task!

# Static Analysis

Now in this section is where we really start to get our hands dirty! We start looking at some more assembly and leverage our tools to help us figure out what the binary may be doing. I am not going to go into how to use each of this tools, but more of what you can expect to learn from this tools, a high level overview of the workflow, and an idea of what they bring to the table.

## Tooling

```
- PEBear
	- Header file info, functions, strings
- Binary Ninja / Ghidra / IDA
	- I often throw the file to all 3 of these tools, because each tool performs different heuristic analysis and each may come up with some interesting stuff. The downside is that the analysis, depending on the file size can take a while, and time is money!
	- Commenting instructions / Blocks
		- As we are going through the code and start to figure out what something doing, we should start commenting
	- Renaming variables & functions
		- This will helps us to better make sense what the code is doing.
			- For example, if we figure out that function is being passed a key, and an input and it has some loops with xor magic, the it is very likely that this may be a encryption or decryption function.
				- We can rename it something like decrypt_func() and rename the variables to key and input_str
	- Highlight instructions and interesting blocks
		- This programs have way to color code and highlight instructions/blocks, we can use that to bring attention to important code that we are interested in. You could also use it to show the flow of the program that you would like to explore and more.
	- Patching instructions
		- There may be times when you are interested in the behavior of the program if it did not take a branch or if it went in a particular flow. If you can detect this, it is often faster to patch that instruction to have the code flow that way and see what the binary does dynamically, rather than spending extra time doing it statically. You have to learn to choose your battles!
	- I also use bookmarks on sections of code. This saves me time when I have to come back and review it, as well as helps me keep track of important code.
	- You could leverage the tool's scripting engine, which could help you get to your goal faster.
	- Leverage the use of the decompiler, it can certainly help you speed the analysis. This gives us pretty close to working C code!
- Writing Code
	- As I go along I usually use VSCode in conjunction with the code to attempt to replicate some interesting code, as it may help me make faster sense of it or just make sense of it altogether!
		- Depending on the complexity of the code, I may use Python for quick prototyping and sometimes it is more complex it just useful to go to C or Golang to better match what the code is doing at each step.
- DnSpy / iLSpy (.Net)
	- This tools are used to disassemble .Net code which include C#, F#, and Visual Basic.
- APKTool (Android Stuff)
	- I do not do much Android stuff, but this is the tool for that!
- Carving
	- This is more like a technique to be aware of. There is times when you have to "extract" what you need out of a file (bytes) and you can do that by what is called carving. You can do it using a tool like dd in Linux or using HxD. I have some shell scripts [here](https://github.com/ManuelBerrueta/RepByteSh) to help with just that task.
- HxD Hex Editor
	- Sometimes you just need to do some quick patching or playing around with bytes, and this is my favorite hex editor for that. It also allows you to carve out files by selecting the bytes and saving them to a file.
- Binwalk -e <fileName>
	○ binwalk is a very versatile tool and one of my favorites. If you have something that may be packed/compressed, binwalk extract gives it a good try at extracting it all.
```

# Dynamic Analysis

## Tooling

Debuggers - WinDbg - x64bbg - OllyDbg - Immunity Debugger - GDB ○ PEDA + GEF + PWNTOOLS § Cool tools but I've had mixed results with more advanced binaries - DumpIt (Dump the whole OS memory) - Process Explorer - Volatility - Rekal

\*\*Dynamic analysis triage: \*\* We can extract the strings running in memory which may give us some quick low hanging fruit goods! For this task we can use Process Explorer. We can also memory dump the whole process with the same tool . This may or may not be as helpful!

We can use Rekal and Volatility to examine the memory dumps! ##Rekal

```bash
rekal -f mem_dump_file.dmp #Load/Open the memory dump
Imageinfo # Display time & date
netstat # Show active connections at the time the memory dump was taken
netscan # Get the pid
pslist  # Proc list
modules # Lists drivers, sys files, etc..
dlllist <PidNum> # Shows all DLLs loaded by that Proc
filescan # Display a files list that each of the procs had open
pstree #Display the proc tree
pedump # Dump code of a running pid to a file
```

## Windows Debuggers

As far as debuggers go, on Windows I usually use x64dbg, and I have had very good luck with it. OllyDbg and Immunity Debugger are not really updated anymore, but they are well known and reliable. WinDbg is the way to go in the long run, but it is not as intuitive to work with until you get to know it and play around with for a bit. This is a tool where I personally need to improve my skills!

## Linux Debugger

GDB is the name of the game in Linux. You can add extensions/plugins such as PEDA, GEF and PwnTools. Your mileage may vary with the extensions!

## Debugger Workflow

The debuggers allow us to run the binary, set break points, look at memory + registers, and manipulate the program including variables to track what the program is doing. You will usually hit a break point at the beginning of the program and you can step in and step over instructions from there. You can look for places where a certain interesting string is called, or a function, that way you can analyze it dynamically and see what is happening in memory as the process runs.

# Closing Notes

As I mentioned in the beginning, as I go along with my analysis I am taking notes of my findings. This saves me time and helps me not to repeat work that I have already done. It also helps me to paint a picture what is happening. Often, by reviewing my notes I can hone in on things that may be worth taking a deeper look. Additionally, it is often the case that at the end of the analysis I have to create a report. This notes really help in the report writing, especially if you develop a system that flows nicely into a report format. This is also the case when doing pentesting/security testing, so once again….take good notes ;)

# Resources

Here is some quick ones, but check out my [resources](https://revx0r.com/resources/) page, I have quite a bit more there! My overview intro to [RE YouTube videos.](https://youtube.com/playlist?list=PLR9QN6c2fXZptdZNlkZNRjaT5GGoFPCrF)

Books: • Practical Malware Analysis • Hacking: The Art of Exploitation

PE files:

- [PE File Info](http://bytepointer.com/resources/pietrek_peering_inside_pe.htm)

Memory Analysis: [Memory Dump Analysis](https://cqureacademy.com/blog/forensics/memory-dump-analysis)
