---
title: "Hunting in EVTX Event Logs"
date: "2022-07-18"
tags: 
  - "blueteam"
  - "cybersecurity"
  - "dfir"
  - "eventviewer"
  - "evtx"
  - "hacking"
  - "infosec"
  - "loganalysis"
  - "loganalytics"
  - "logs"
  - "pentesting"
  - "powershell"
  - "redteam"
  - "security"
  - "threathunting"
  - "windows-2"
  - "windowseventlog"
---

So, you've come across an EVTX (`.evtx`) log file which you need to analyze or get some useful information from... I know the feeling!

### Tools

**`Event Viewer`**  
You can use the Event Viewer, the GUI program built into Windows which is great but if you have a ton of events...well it can be painful

**`PowerShell`**  
Here is our fiend PowerShell to help us in this journey!  
Using `Get-WinEvent` FTW!

**[`DeepBlueCLI`](https://github.com/sans-blue-team/DeepBlueCLI)** `a PowerShell Module for Threat Hunting via Windows Event Logs` provided kindly to use by [@eric\_conrad](https://twitter.com/eric_conrad) and the great folks at SANS. This program is able to crunch to .evtx files to look for goodies.

## The Story…

I was trying to look at some `evtx` files and extract some useful goodies out of it…  
While I could use `Event Viewer`, it is unfortunately not the quickest way to skim through these logs to find things that I might be interested in, and speed was of the essence. So, what did I do? What every good hacker does of course, reach out to his search engine friends :)

I looked around to see what could be useful/speedy quick to get to the hacking! I found this useful article from SANS [DeepBlueCLI: Powershell Threat Hunting](https://isc.sans.edu/diary/DeepBlueCLI%3A+Powershell+Threat+Hunting/25730) by Russ McRee([@holisticinfosec](https://twitter.com/holisticinfosec)) which in turn led me to `DeepBlueCLI`. But unfortunately for me, it didn't quite work out, because of the events that I was looking at. However, for most of the `Threat Hunters`, `Blue Team`, `Defenders`, and `DFIR` side, this `DeepBlueCLI` might be really useful and do recommend for you to check it out.

So, what was my next step? Use `Get-WinEvent` in `PowerShell`. So, I created a quick and dirty script (if you can call it that) to look for some goodies. It is a good starter for your hacking needs and should be fairly easy to modify for getting at the information you are wanting to get!

### Get the script...

Snag it from my [BST](https://github.com/ManuelBerrueta/BST) GitHub Repo: [https://github.com/ManuelBerrueta/BST](https://github.com/ManuelBerrueta/BST), the script is in [/Windows/Search\_EVTX\_LogEvents.ps1](https://github.com/ManuelBerrueta/BST/blob/main/Windows/Search_EVTX_LogEvents.ps1)

#### Running the script

`.\Search_EVTX_LogEvents.ps1 -TargetEvtxFile <\path\to\YourEvents.evtx>`
