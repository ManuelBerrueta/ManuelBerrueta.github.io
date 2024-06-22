---
title: "Installing VSCode extensions from a list"
date: "2020-08-15"
tags: 
  - "vscode"
---

I was building a new VM for doing some security testing and one of the most boring things to do when I build a new one is putting all my tooling together.

One of my favorite tools is VSCode. I like it's versatility, the fact that I can use it with any language it's something I really like. Another I hate doing is installing the extensions, and I wanted to see if there was a way to automate this.

I found that vscode supports cli and you can install the extensions via the command line using the code 'code --install-extension <extensionName>' command. The caveat is that it can only take one argument at the time at the time of this blog post anyway. So I thought about a script, but I found one on Stack Overflow before I even got started writing it. I modified the scripts to take a file with the extension names as an argument.

To backup your current extensions to feed it to the script use the command:

```
code --list-extensions > VSCode_Extension_List.txt
```

Here is the script for Linux:

```
#!/usr/bin/env bash

cat $1 | while read extension || [[ -n $extension ]];
do
  code --install-extension $extension --force
done
```

Here is the script for Windows using PowerShell:

```
Get-Content $args[0] | ForEach-Object {code --install-extension $_}
```

I have tested the Linux one, but not the PowerShell one. In theory it should work!

Reference:

[https://stackoverflow.com/questions/58513266/how-to-install-multiple-extensions-in-vscode-using-command-line](https://stackoverflow.com/questions/58513266/how-to-install-multiple-extensions-in-vscode-using-command-line)
