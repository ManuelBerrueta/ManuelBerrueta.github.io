---
title: "Persistent MAC Address Spoofing"
date: "2019-07-13"
categories: 
  - "wifi"
coverImage: "Macchnagerizer.png"
---

Recently while doing some testing I wanted to see if my mac address was indeed being spoofed via the default settings of Parrot OS (my current distro of choice for development and light security testing). So I checked my mac address using `macchanger -s` prior to connecting to the access point, which indeed showed that my mac address was being ‘spoofed’. However after I connected to the access point, I rechecked my mac address using the same command and to my surprise it showed that it reverted back to my actual physical mac address. That was not going to cut it and I had to look into it.  
At first I thought it might have something to do with the settings being applied while the adapter was up, so first I tried just bringing the adapter down using `ifconfig wlan0 down`, and changing the mac address using `macchanger -a` and bringing the adapter back up using `ifconfing wlan0` up. I tried connecting again and it turns out that it gave me the same result, it had reverted back to the physical address =(.  
After further research I found that I had to bring the network manager service down as well and after some experimentation with this I came up with the following recipe and decided to make this into a shell script which I cleverly called Macchangerizer.sh to take care of this for me, since I try to follow the rule that if you do something more than once, automate it using a script.  
  
You can downloaded it from my repo @ [https://github.com/ManuelBerrueta/Shell\_Scripts](https://github.com/ManuelBerrueta/Shell_Scripts)
