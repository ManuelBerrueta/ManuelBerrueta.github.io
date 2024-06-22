---
title: "Cryptanalysis A LA CTF"
date: "2019-07-13"
categories: 
  - "crypto"
tags: 
  - "crypto"
  - "cryptography"
  - "ctf"
  - "cybersecurity"
  - "hacking"
  - "hackthebox"
  - "htb"
  - "infosec"
  - "oscp"
  - "pentesting"
  - "security"
  - "tryhackme"
coverImage: "CryptanalysisALACTF.png"
---

I know there are a lot of people that struggle with “crypto” CTF challenges and I thought I would share how I approach them as it may help you with future challenges. This is also a work in progress and I may update this in the future.

#### 1\. List of strings, figure out which one contains the flag and decode

The first of these challenges that I want to talk about contained a list of strings, one of those strings contained the flag. While I could have tried to use a brute force approach and try to decode each one, not only is it too time consuming for my taste =) , but you also have to figure out what type of encoding is being used…and yes I could write a Python script and cycle through the strings and see which one contains the flag, but that would require me to write a Python script to cycle through the strings to see which one contains the flag…yes I am repeating myself, I know!

What I am saying is that we can use a more elegant and methodological approach. To do this let’s start by taking a look at the problem in more detail. In many CTF challenges your flag is given in a format along these lines “flag{ThisIsTheFlag}” , in this particular problem it is given that our string will contain “flag” as part of the string. It is also well known that many of these strings are just simply obfuscated/encoded or “encrypted” (if you will) with a Caesar Cipher, Base64, Hex, xor among a few others.

In this case the strings were pretty short, thus it didn’t look like Base64, contained non hex ASCII characters, and I took a pretty good educated guess that we were dealing with a simple Caesar Cipher.

With that in mind I started looking at the strings using the fact that a Caesar Cipher is just a simple shift in the letters and that the string contains the word “flag”. My next step was to look at the first 2 letters in each of these strings for an offset of 6 from each other, because l is 6 letters away from f, which are the first two letters in the word flag.

Shockingly enough I found a string fitting this pattern! Now I just had to figure out the shift, this was the easy part! For example if my flag was “jpek{XlmwMwXliJpek}”, I know that my first letter is f which equals 102 and I know that j == 106, and 106 – 102 = 4 or simply that j is 4 letters up from f, thus my shift is 4.

Now you can either use a Caesar Cipher decoder online or write your own, which is what I did below to get my flag:

https://gist.github.com/ManuelBerrueta/104e8a22baad6708432a963444eb14ba

#### 2\. Given a list of names ending with numbers, can you figure out the encoded message

This was upon inspection you could see that none of the numbers went over 26 (the number of letters in the alphabet), I figured each number belonged to a letter in the alphabet. That was the ‘hard’ part, now all we have to do is decode the message. Here is an example list below:

Alice (8, 5, 12, 12, 15)

Eve (26, 15, 13, 2, 9, 5)

Bob (23, 15, 18, 12, 4)

I found a table with letters and their corresponding value [here](http://moonatnoon.com/puzzles/reference/a1b2z26.html). I started to manually convert the letters, but because it is not the first and I am sure it won’t be the last time I see a challenge like this, instead of manually converting each letter I decided to make the python script below.

I copied the letters from the table and I appended them to a hash table(dictionary), where the key is the integer ‘value’ of the letter. I then copied the number above into a list and added a 0 for a space in between each of those sequences of integers. I then used the values from the list as my key values in the hash table and printed out my flag, “HELLO ZOMBIE WORLD”

https://gist.github.com/ManuelBerrueta/ea5622d5cd76ae234eb76f70ce32d2d1

#### 3\. Given some RGB colors can you figure out the code

Similar to the last one we are given a list of RGB colors. If you have never worked with RGB colors, each color is represented by 3 values ranging from 0-255. For example 255, 0, 0 represents Red=255, Green=0, Blue=0, thus giving us max amount of red.

To solve this one I simply put all the values of the colors in a list and converted each value to ASCII, pretty simple one.

#### 4\. Arnold Cipher

The Arnold Cipher is one in which you are given a list of numbers and a book or pages of a book. Each number is separated into 3 parts and you can think of each number on the list as a coordinate on a page of the book.

An example of one number would be 05.10.07. The way the number is broken down is as follows: Page\_Number . Line\_Number . Word\_Number

The page number simply tells you what page in the book, the line number is the line starting your count from the top down, and the word number is the word you are looking for starting your count from the left. You do this for each of the numbers on the list to put a phrase together and find your flag.

One of the things I do is I take pictures or screenshot of the pages. I then number the lines so it makes it easier to go to the right lines and don’t have to count each time. I find that helps me solve them a lot faster.

#### 5\. Columnar Transposition

A Columnar Transposition Cipher is one where it is given various columns of text and you have to rearrange the columns position in a way that you can make sense of the words. Once you figure out the right combination, you can attain the flag.

This one is not complicated. They way I go about approaching this problem is to try to look for one word that I can rearrange those columns to make sense out of, or look at the top word and see if I can figure out what that might be and rearrange the columns respectively.

#### 6\. Steganography

Instead of me trying to explain or define steganography, I think the definition from Wikipedia is best:

Steganography is the practice of concealing a file, message, image, or video within another file, message, image, or video. The word steganography combines the Greek words steganos, meaning “covered, concealed, or protected”, and graphein meaning “writing”.

Most of the time you are going to be just dealing with an image that has something hidden. I have ran into different cases , one is where the picture has files embedded and you have to extract them, another is where the flag is embedded within the file, and then another where the image it self has the flag in the visual and you have to change the opacity or adjust the image to get the flag.

The first thing I do is download the image and run the file command. That might yield some information. However, the file command looks mostly at just the file header, and sometimes you have to dig a little deeper, so I tend to also use binwalk and see what it returns.

Binwalk is a tool that was designed for firmware analysis, reverse engineering and extraction. Very fun to use if you are ever dealing with firmware =). One of it’s most popular feature is it’s signature scan, which is pretty handy when you are dealing with files such as steganography challenges, because it will scan the whole file to see if it can find other signatures embedded within the file.

If you do find something like a compressed archive you might want to try a tool like 7-Zip to see if it is able to extract it. There might be other times where you might have to carve the file out of the file/image, this can be done using the dd command in Linux. I have also made some scripts to remove, replace, and carve out sections of a file when I was doing some firmware research and are available in my GitHub [here](https://github.com/ManuelBerrueta/Shell-Scripts/tree/master/repByte). I have to warn you about using the ddcommand, you can do a lot of damage if you don’t know what you are doing, so proceed with caution!

I have a few more tricks for steganography . You could use the strings command to list all the strings in a file which may reveal the flag, if you know the word flag is part of the whole flag you could pipe it into grep to search specifically for that strings like so strings fileTarget.jpg | grep flag . You could also use the xxd command to look at the file in a hex and ASCII combination. Last but not least you can run the cat command on the image, which will attempt to read the file as a text file and you might be able to see the flag embedded in the file structure as bytes. This one is easy to do as well and can yield fast results.

Alternatively there is also the steghide program which you can install using your package manager. With this program you are able to hide and extract data from an image file.

Last note on steganography is that this technique is actually used in the wild by cyber crime such as malware to get information such as a C2C address and to execute commands.

#### 7\. Atbash Cipher

[https://crypto.interactive-maths.com/atbash-cipher.html](https://crypto.interactive-maths.com/atbash-cipher.html)

#### 8\. QR Codes

#### 9\. Morse Code

If you run into Morse code the site below has a pretty nice decoder

[https://morsecode.scphillips.com/](https://morsecode.scphillips.com/)

#### NSA’s Crypto Challenge

For other challenges such as the [NSA’s Crypto Challenge](https://www.cryptochallenge.io/), you can still use a methodological approach. Again we look at the problem we are trying to solve.

In this challenge the letters are not shifted, but they are replaced by another letter. For example, given “CLAP”, let C=D, L=U, A=C, P=K, and thus you get “DUCK.” In this case one of the things that can help us is the frequency of letters in the English language. For this I like to use [Wikipedia’s Letter frequency](https://en.wikipedia.org/wiki/Letter_frequency).

Now we can take a look at the letters that appear the most in the challenge and start exchanging those with some of the common letters until you start putting word together. Use other common tools such as a search engines to find words that end with whatever letter, or common 3 letter words, etc.

It also helps to keep track of the combinations you have tried in your favorite note taking application. That way you don’t keep going around in circles with combinations that you have already tried.

In conclusion, if we take a minute to look at the problem we can usually find a methodological way to approach it. This will increase our chances of success and give us a sense of direction because we have some kind of structure in place.

#### Resources

Cracking hashes:

[https://md5hashing.net/](https://md5hashing.net/)

Cipher Tools:

[http://rumkin.com/tools/cipher/](http://rumkin.com/tools/cipher/)

Dcode Tools:

[https://www.dcode.fr/en](https://www.dcode.fr/en)

Steganography

[https://ctf101.org/forensics/what-is-stegonagraphy/](https://ctf101.org/forensics/what-is-stegonagraphy/)

[https://ctfs.github.io/resources/topics/steganography/README.html](https://ctfs.github.io/resources/topics/steganography/README.html)

ASCII Table

[http://www.asciitable.com/](http://www.asciitable.com/)
