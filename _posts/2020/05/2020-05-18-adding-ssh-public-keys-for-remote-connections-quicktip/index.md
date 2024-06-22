---
title: "Adding SSH public keys for remote connections - #QuickTip"
date: "2020-05-18"
---

Let's say you want to connect to your remote Linux server/machine. You can use your current ssh keys to do this, this allows you to connect more securely and without a password (assuming your keys have not been already compromised =)). Although you can also use a combination of both, an ssh key and a password/passphrase, I won't be covering that scenario right now.

To be able to connect using your keys:  
\- Create a ~/.ssh/authorized\_keys file (if it doesn't exist already)  
\- Simply add your public key to this "~/.ssh/authorized\_keys"  
\- - For this step you can do it a few different ways, but one of the easiest is to use your favorite editor i.e. vim, nano, gedit, etc...

What if you want to remote in from multiple machines? I am glad you asked! You follow the same procedure as above just add the key below the already existing ones, or you can use the following command to append:  
echo "ssh-rsa ABCDE...YOUR PUBLIC KEY" >> ~/.ssh/authorized

Now you can remote in from multiple machines easily =)
