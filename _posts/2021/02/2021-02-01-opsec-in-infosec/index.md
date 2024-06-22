---
title: "OPSEC in InfoSec"
date: "2021-02-01"
coverImage: "lossy-page1-307px-_WAAC_-_SILENCE_MEANS_SECURITY__-_NARA_-_515987.tif.jpg"
---

While reading the blog posts from Microsoft and Google about the targeting of security researchers (links are listed in the reference section), which with high confidence are linked to the DPRK, reminded me of something I was taught in my time in the military, that is "Operation Security" or "OPSEC" for short.

What is OPSEC?

"Operations security (OPSEC) is a process that identifies critical information to determine if friendly actions can be observed by enemy intelligence, determines if information obtained by adversaries could be interpreted to be useful to them, and then executes selected measures that eliminate or reduce adversary exploitation of friendly critical information.

In a more general sense, OPSEC is the process of protecting individual pieces of data that could be grouped together to give the bigger picture (called aggregation)" definition sourced from [https://en.wikipedia.org/wiki/Operations\_security](https://en.wikipedia.org/wiki/Operations_security)

OPSEC is something that I try to exercise my self with my own work, it is also something that pops into my head whenever I am approached by unknown to me entities. Working in security makes you a target of interest, this is not a surprise to me, and it should not be to you. If you think about it, we work with material of sensitive nature and often things that we can not share with the public. This makes us a perfect target, since our knowledge could be leveraged by the adversary.

Looking into the details of the posts, what was most interesting to me was their modus operandi, the way they operated to engage their targets. They put out "high quality content", built up their reputation, and gained traction with followers. This made a great setup for their social engineering attack. Then they proceeded to engage targets by communicating with them, following up with links to malicious site(s) and Visual Studio projects. They maintained their own operation security by being meticulous about their work, including changing characteristics of the files they were exchanging enough in an attempt to avoid "static IOCs". Further when the MHTML page was loaded, the JavaScript was ran and beaconed to the mothership for further code instruction/execution.

According to the Google TAG (Threat Analysis Group) post, they were not able to "confirm the mechanism of compromise". Reading between the lines, since the compromised systems (or most of them) were running up to date patched versions of Windows and Chrome, there is a possibility that they may be using a 0 day (previously unknown vulnerability being exploited) to compromise this systems.

Dissecting this, make no mistake that the adversary was committed and invested in the attack. They took enough time to plan this out and use their tradecraft (in what it seems to me) to no different extent than they would target an organization or government entity. While I am no expert on the subject, I have been tracking the work of APTs for a while and this adversaries operate much like an enterprise in that they look at the ROI (Return of Investment) and security researchers seem to be worth that investment and now its more obvious that the security community is on the menu.

I really enjoy being part of the information security community and I have learned a lot from it, continue to learn from it and also like to share to help others. While doing so we should be more aware of the folks that are approaching us and with the information that we share. We should learn to recognize that there are people out there interested in our work, but their interest may not be of the best intentions, and should proceed with caution.

My conclusion is that we should not get complaisant ("complacency kills" is a common term in the military) and we should operate with an appropriate level of operation security. Keep your eyes and ears open, and keep on keeping on that security mindset!

_If you have not read these posts, I highly encourage you to read both the Microsoft and TAG posts. The Microsoft post is much more detailed and includes a lot more information (IOCs) that might be useful, while the TAG posts is brief. I also encourage you to read the ZDNet post which includes Twitter posts from some of the researchers targeted._

### **REFERENCE:**

- [ZINC attacks against security researchers - Microsoft Security](https://www.microsoft.com/security/blog/2021/01/28/zinc-attacks-against-security-researchers/)
- [New campaign targeting security researchers (blog.google)](https://blog.google/threat-analysis-group/new-campaign-targeting-security-researchers/)
- [Google: North Korean hackers have targeted security researchers via social media | ZDNet](https://www.zdnet.com/article/google-north-korean-hackers-have-targeted-security-researchers-via-social-media/)
