---
title: "Hyper-V Enhanced Session Issue"
date: "2020-11-06"
---

I have been using VMware workstation for a while, but it has been super slow lately. I am not sure if it is because I have Hyper-V enabled and using WSL2 that may be causing the slow downs...but either way, I have decided to move over to Hyper-V.

I have had good luck with it for Dev work, so now I am going to try to use it as my main virtualization software.

I made this post because when running a Windows 10 VM (I have not made a Linux one yet...) the enhanced session showed a blurred sign in screen and I could not get it to show the username and password boxes to sign in, but the Basic session would work just fine.

## How did I solve the problem?

After some troubleshooting I figured out that what might be causing the issue is Windows Hello. It was enabled by default and I could not turn it off because it was grayed out. To solve this I setup a Windows Hello Pin. After that I had to close this settings page and reopen it. Once I was there again I was able to turn off the "Require Windows Hello sign-in for Microsoft accounts". I closed the session and opened it again...SURPRISE, enhanced session is now working!
