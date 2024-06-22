---
title: "Pktmon.exe - #QuickTip + #ToolTip"
date: "2020-05-19"
---

Bleeping computer released this article "[Windows 10 quietly got a built-in network sniffer, how to use](https://www.bleepingcomputer.com/news/microsoft/windows-10-quietly-got-a-built-in-network-sniffer-how-to-use/)" about a built-in packet sniffer that was introduced in the October 2018 update of Windows 10 called pktmon.exe.

I wanted to check it out and put it through its paces. The first thing you have to know is that it requires admin permissions to use it. I like to stay on the keyboard as much as possible and I am pretty confident with the command line so I am going to show you an extra tip. I like using PowerShell for Windows related command prompt stuff, so the way I start PowerShell with Admin without taking my hands off the keyboard is like so:  
1\. I hit the Windows key  
2\. Type PowerShell  
3\. PowerShell should be highlighted now, hold CTRL + SHIFT together and press enter  
4\. UAC will pop up (If you haven't changed this of course), press the left arrow key and then press enter.  
5\. Now you have an Admin PowerShell

If you just type pktmon you will get the same output as pktmon help, which is a list of the commands. If you type pktmon <command> help, it will list the options of commands for that command. Thus you can stack commands with the help command at the end to get an idea of what a combination of commands will do.

One of the first things you need to know is the Id of the adapter you want to listen to, type the command pktmon comp list to show you a list of all the adapters.

Now let's target port 22: **pktmon filter add -p 22**  
In my case my id for my WiFi adapter is 12: **pktmon start --etw -p 0 -c 12**  
Attempt to an SSH connection to generate some traffic.  
Then type pktmon stop, to stop the capture.  
Last reset your filters by: **pktmon filter remove**.  
You can verify before and after what filters you are using by simply typing **pktmon filter list**, thus after the remove you should see "There are no packet filters."

If you try to look at the Pktmon.etl capture file it created you just see a bunch of non human readable data. We can convert it over to a txt file to get some information by using the command: **pktmon format PktMon.etl -o ssh.txt**. We can now get some information about the traffic. However, this is very limited and as Bleeping Computer mentioned in the article, you are better off downloading the Microsoft Network Monitor to be able to look at the packets in a WireShark type manner.

Reading on the updates of the article, it sounds like soon this tool will receive an update which will allow you to convert the captured traffic from the .etl format to a PCAPNG format, which will be really useful to be able to look at in WireShark using the filters and built in tools.

I think we could look at this tool at becoming the tcpdump of Windows.
