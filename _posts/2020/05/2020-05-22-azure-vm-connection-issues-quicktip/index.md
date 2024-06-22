---
title: "#Azure VM Connection Issues #QuickTip"
date: "2020-05-22"
tags: 
  - "azure"
  - "quicktip"
coverImage: "Azure_Connection_Troubleshoot.png"
---

If you are having issues connecting to a new VM, Azure has a really cool built-in tool, you'll never guess what is called, "Connection troubleshoot". It it on the left hand side menu bar and I have included a screenshot below.

I am bringing this up because I ran into an issue where I setup a new VM and I was having issues connecting to it, I have setup numerous VMs before it has always been fine, but this time not so much!

By using this tool it can point out issues and/or contradictions in rules in the Networking Settings. I had an allow rule for SSH, but another rule was applied that contradicted the rule. For security reasons the deny rule will be the dominant rule and thus I was not able to connect. Thanks to the tool I was able to pinpoint my issue fast and effectively and now I can connect and continue on!

I feel like this may be a a good tip to keep around just in case other people run into snags or to remind my self how I fixed it =)

![](https://i0.wp.com/revx0r.com/wp-content/uploads/2020/05/Azure_Connection_Troubleshoot.png?fit=1024%2C738&ssl=1)
