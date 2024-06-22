---
title: "Security : Doing The Basics Right"
date: "2020-01-01"
coverImage: "security_bacics.png"
---

Now days a security incident i.e. breach/attack seems like everyday news. Many of the times when we look at where it was that security failed, it's usually on some of the basics.

It is true that there is a human factor involved many times, like a phishing email, someone downloaded something, or visited a malicious site. However, after that happens, that is only one machine that got compromised at first, which is often used to pivot to other machines. There is a high chance that we missed some of the basic security fundamentals to isolate the incident.

To me, the very first things to do before you spend any money on fancy tools for protecting your network and endpoints, it's very fundamental...KNOW and UNDERSTAND your network. But, what does this really mean? This means you know what devices are on your network, and spend the time to understand it. Get a baseline of what your traffic looks like, that way when an anomaly arises you can spot it more efficiently. You should have your self a network diagram, where you have details about your endpoints. This includes printers, computers, monitoring devices, test equipment, switches, routers, [Raspberry Pi's (Yes, look at what happened to NASA)](https://www.cnet.com/news/raspberry-pi-hack-puts-nasa-in-security-jam/), etc. Do an audit on the devices on your network, do all those devices need to be plugged in? Is there any devices we don't use anymore? You can reduce your attack surface in that way. Know what services are running on each of your endpoints, is that normal? Do you need all these services turned on? Is communication between a desktop and server normal? How about communications between desktop to desktop? Understanding your communication flow gives you visibility. To gain visibility you must be logging as well, are you doing that? It will allow you to act faster when you notice abnormality within the network, because you will be able to identify the [IOCs](https://digitalguardian.com/blog/what-are-indicators-compromise) a lot quicker. You have to remember you can't secure what you don't understand.

Now days,you should be thinking in a deny by default mindset, that is white list applications, hosts and deny anything else. This can be an underwhelming task, but it can pay dividends in the event of a compromise.

As you go along always remember that security should be part of your process, not a second hand option. Security is not free, you have to put in the work and you need talent to help you get there. Training people is important. I am not just talking about your security team, but your inside customers as well. That is, those people using the end points, they need training as well on the emerging threats. You could take sometime and show the people some of these threats, what they should be looking for, and what to do in case of an undesired event. Take for example a billing and HR department, and show them a real threat of something they may encounter as part of their daily work, i.e. if you enable macros on a word document and if it happens to be malicious, you could have your self ransomware and/or a compromised endpoint. Don't just stop there right, tell them what to do if this happens, what is your procedure? What would you like the user to do? May be the answer is to call your security department immediately or unplug the machine off the network? These are the type of training you need to do and things you need to think about before they happen.

Are you segmenting your network? Segmenting your network could help you contain a breach. Audit user's permissions, giving them access to the resources they need. Identify for your important data and intellectual property. You should probably have back ups of these stuff, after all it is important. How are you protecting it? Who has access to it? and very importantly who needs and should have access to it?

Physical security should be a part of your threat model. You can have all kinds of security in place, but if someone can come in and plug a device into the network, well then they just bypassed a lot of what you had in place.

Patching....do you do it? If so, are you just patching Windows? How about the routers and switches? Printers? The attacker looks for the weakest link, and they only have to find one. This fact is one of the reasons that makes security hard.

I think practicing defense in depth and taking a layered approach to security is a good way to approach the problem. As we look at our assets we constantly need to think how can X affect confidentiality, integrity and availability. If you follow, you can see that it comes down to risk management. After all we have to try to get security right all the time, the attacker only needs to find one weak link. Let's start with the basics and make it harder for them!

My 2 cents, Happy New Year!
