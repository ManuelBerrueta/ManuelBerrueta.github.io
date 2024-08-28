---
layout: post
title: Proxy VM Traffic to Host Burp Suite
categories: Burp Proxy Tools
---

### Proxy VM Traffic to Host Burp Suite
Recently I had the need to proxy the web traffic from a VM through Burp Suite in my host for some security testing and I ended up going into a bit of a rabbit hole to get it to work. I figured I would make a note for myself and save some time next time, as well as for others that might have to do this in the future:

1. In your VM, go to the Proxy settings and put your host **IP Address** of your host & the **Port** number that Burp is listening on (usually `8080`).
2. Proxy Listener Binding: 
	1. In Burp @ your host machine:
		1. Go to your Proxy Settings (Tools > Proxy > Proxy listeners) and click "Edit" to your listener (or Add a new one if you don't have one).
			1. From the "Bind to address:", select "All interfaces"
3. Certificate: 
	4. In Burp @ your host machine, in the Proxy Settings, export the CA cert.
	5. Or optionally navigate to `http://burpsuite` and download the CA cert.
4. Copy the Cert over to your VM and install the cert in "Trusted Root Certification Authorities".
	1. Note if you are using Firefox you have to Add it to its settings since Firefox doesn't use the systems CA root store.
5. You should be good to go and be able to see the traffic now 🙌
