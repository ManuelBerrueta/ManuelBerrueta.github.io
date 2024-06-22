---
title: "RE .001 - My RE Methodology"
date: "2019-07-13"
coverImage: "RE001Binja.png"
---

Noticed the title of this blog post is RE .001 not 101! There is much better intros that dive right into reverse engineering using the tools and so on. The purpose of this is to give you a basic idea of how to approach it, not necessarily how to use the tools. Reversing requires you to have a certain mindset and in my opinion it requires you to approach it methodically.

I am going to share the way I approach reverse engineering whether it is for a work given assignment, working on a CTF, Malware, etc...

The very first thing I do is get my reverse engineering VM setup. I used to custom build my own, but FireEye has set up a pretty cool VM with most of the tools I use and more. It is called the [FLARE-VM](https://github.com/fireeye/flare-vm)  and with minor tweaks and some of my own personal tools I have been very happy with it so far, big thanks to FireEye. As a side note FireEye just released Commando VM which is their Windows based pen testing platform.

I fire up my "root" snapshot and I update my VM and any of the tools I will be using. If I need any extra goodies then I go ahead and bring them in at this time. Before I start working in the VM after it is up to date and ready to go the way I want it, I either delete my older "children" snapshots or just fork another one from my clean updated version.

After I am done spinning up my new clean VM snapshot, I drop the file I am going to be work with into it. Before I run the file, one of the first things I do is head over to [virustotal.com](https://www.virustotal.com/) and I either upload it or run a hash on it. There are reasons to do it one way or the other, but I won't go into that here. There are also times where you might not want to do this it all because it might give your adversary a clue that they have been detected and you have begun to do triage on the systems.

At this time before I do anything else I isolate my VM from the internet, very IMPORTANT, especially when dealing with suspicious files or malware, after all you don't want to get any surprises =).

Now I run: file myFileName and see what I can get out of that. After that depending on what I got out of it, I usually run one of my favorite tools, binwalk on the file and see if I can get anything juicy out of it. binwalk goes through the file header, and the whole file and tries to identify other file headers for possible embedded files within a file. With firmware as well as other files you might find filed embedded which you might be able to scrape out by using binwalk -Me fileName, this extracts the files recursively. binwalk tends to do a really good job at extracting everything it can out of the file. I also often check the entropy of the file for possible packing/compression/encryption. You can do this by using binwalk -E fileName and it will graphically show the entropy of the file which is pretty nice, but you can alternatively do this with a little python script.

After that I run strings on the file to see if anything interesting catches my eye. Such things as IP addresses for a C2C, or may be an encryption key, and if you are playing a CTF may be a flag{}. I might do some basic analysis using xxd and see what I can find peeking around.

If the file is packed you can use something like PEiD to see if it can identify the packer, and thus unpack it. If it is compressed you might be able to decompress it with 7zip or tar. If some of it is encrypted, then well you might have to run it and dump the memory to reverse it.

You can further checkout the file by using dependency walker, PE-bear, PE Explorer, PEview, CFF Explorer, etc. With these tools you can look at the header, look at the imported and exported functions, and at all the different sections of the file.

Assuming that the file is not packed or encrypted, we can now get started with some static analysis. I currently use a mix of IDA Pro and Binary Ninja, each one has it's own features that I like, and so I use them back and forth. The thing that does suck about doing that is that I end up doing double the comments and things of that nature. There is a new toy out, Ghidra, and I have not had much of a chance to play with it, but the disassembler is pretty good, especially when compared to other ones such as snowman. I can definitely see it becoming part of my workflow at some point.

As I start looking at the code, I like to peek around a little bit to get an idea of what is happening.  Depending on what it is that I am tasked or trying to do is the way I will approach the problem after my initial "inspection." There are times where I need to translate the code over to C do something, in this case I will just walk the code and start translating over to C. Other times may be I'm working on a CTF so I am looking around for a flag or trying to figure out how to get the flag. There are other times when I just need to get an overview idea of what the program is doing and then decide if we need to dig deeper.

No matter what the task is though, I always start making comments on the code, start renaming variables and functions according to what they are doing. This helps a TON when you are reversing because it helps you walk through the code easier and make sense of it a lot faster. Even if you are unsure is worth taking chances. For example, let's say you come across a function and you think it is decrypting let's say a key, you can rename the function possible\_Decrypting\_Function and there is no penalty if you are wrong! You can always change it later. Even when you are wrong it often helps because as you follow the code you can see that may be is not decrypting but may be the opposite lol. One thing I can tell you is that not renaming and adding comments as you go along is one of the biggest and mostly costly mistakes you can make, as you can lose track of where you were with the code, if you have already visited that segment of code, and even lose some important information because you might forget something.

As I am walking through code I make notes as I often have to write reports and also because it helps me keep track of things. I used to use OneNote because of the way you can structure things and they syncing capabilities. Then I switched to CherryTree because it has a tree like structure for doing notes, but sometimes things get messy. I now switched to Google Docs for now as I can have my notes anywhere, don't have to worry about losing them and they are available anywhere I go. When I have to write a formal report I usually just use Microsoft Word and my notes really come in handy then!

I also often write code from what I am seeing. I do that sometimes to help me make better sense of what the program is doing, may be to decode or to test something. I like to use visual studio code because is multi platform, tons of extensions available, the integrated terminal and debugging capabilities.

There may be other times when you have to do some dynamic analysis, that is, actually having to run the code for several reasons. In that case I use my favorite debugger, x64dbg most of the time. But I do keep Olly, Immunity Debugger, and WinDbg around. It seems like they all have their place for dynamic analysis depending on certain situations. I do have to work with .NET executables and when I do that dnSpy is money, it has a really nice UI and decompiler as well as a built in debugger. If you are working in Linux than GDB and PEDA are your best options, it is a command line based but once you get used to it is not hard to use.

When I am walking through code in the debugger I get close to what I am trying to inspect and then I start putting some breakpoints on things that might be of interest, for example near where a program crashes, or right before a function that starts allocating dynamic memory.

This debuggers are powerful tools and have a lot of information to offer, but you have to be willing to spend the time to learn how they work and know how to use the information they are providing you to your advantage.

Some of the other tools that I use in conjunction with the debuggers are Wireshark and Sysinternals to help me see what the file is doing as I am running it.

This is my way of approaching it, your mileage may vary. If you are interested in seeing more, may be making this a series or you just found this helpful, please drop me DM on twitter or send me an email at bWFudWVsYmVycnVldGFAZ21haWwuY29t.

Enough for now, more to come...that is...when I have time =)
