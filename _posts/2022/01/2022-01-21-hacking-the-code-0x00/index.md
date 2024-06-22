---
title: "HackingTheCode [0x00]"
date: "2022-01-21"
tags: 
  - "code"
  - "cybersecurity"
  - "ethicalhacking"
  - "exploit"
  - "exploitation"
  - "hacking"
  - "infosec"
  - "linux"
  - "offensivesec"
  - "offensivesecurity"
  - "offsec"
  - "oscp"
  - "pentesting"
  - "redteam"
  - "windows-2"
coverImage: "hello_wold_asm.jpg"
---

In this series/post/document we are going to go through some of the different layers of code hacking/exploitation as well as some tooling. You may consider it a bit on the deep-end, and it does include code.

A fair disclaimer: I am a bit of geek when it comes to this stuff, and I do not claim to know it all, but I am working on understanding everything I share a bit better. By creating this content, I also find that it helps me finds the gaps within my own knowledge. As a side note, how I came to know this stuff is combination of independent research and teaching myself through books, blogs, videos, experimentation, training/classes and also through work experience which is one of my favorite environments to learn and leverage my research skills to get the job done 🙂. Last note, here if you see something that you think to be inacurate, that could use correction or further clarity please reach out. Also, if this helps you in anyway, I love to hear feedback :)

# Some pre-requisites/prior knowledge I would recommend:

- Programming Knowledge
    - We will be working with Some `C`, `asm`, `Python`, and possibly some `Go`
    - Some software engineering fundamentals, especially data structures and architecture
- Operating System knowledge
- VSCode or your favorite code editor
- Linux Fu
- Fuzzing
- Virtualization / Virtual Machines (VMs)
- Reverse Engineering | Static & Dynamic Analysis | Debugging
    - We will be using tools some RE tools such as hex editors, disassemblers, and decompilers.
        - `IDA`, `Binary Ninja`, `Ghidra`, `binwalk`, etc...
    - For dynamic/debugging
        - `gdb`, `x64dg`, `WinDbg`, `Immunity Debugger`, `OllyDbg`
    - Check this [RE Craft](https://revx0r.com/re-craft/) post for some reference of the tooling/workflow/methodology

My goal is to make this as easy to digest as possible so don't get discouraged if you don't know the pre-requisites as long as the material interests you, I encourage you to go through it. You may consider some of the content a bit dry and possibly at times boring, however we must understand the basics and fundamentals before we dig in further. Let's jump right in...

# What’s a program?

We can think of a program, as a set of instructions that tell the computer to do something. Going a bit deeper, we could say that a program is an algorithm, a recipe of sort. This recipe has a beginning/start and it also must end at some point, no different than making your favorite snack :)

### Low Level Languages

At the lower level is your `machine code`, binary raw 1s and 0s. It is often also read as code instructions in hexadecimal. You may also hear things such as op code and shell code.

Then you have `asm` assembly, which is a step above, that is based on simple CPU instructions.

### High Level Programing Languages

High level programming languages are the most common ones that you hear about C, C++, Python, JavaScript, Rust, Go, etc...

Even within these languages you can consider C at the lower level of High-Level programming languages due to the amount of control it has, but with great power comes great responsibility...that and human/coding errors...

They are called High Level because they allow you to use spoken language like syntax to program, instead of 1s and 0s, hex or assembly.

## Anatomy of a C Program

A lot of the important and core back bones of operating systems (\*nix and Windows) were built using `C` (which are still in use today) and therefore a good language to know and understand. This will become clearer (or more confusing) when we start talking about exploits.

Here is a small snippet of a really simple `C` program:

```c
  1 #include <stdio.h>
  2 #include <stdlib.h>
  3 int main(void)
  4 {
  5    printf("Hello World!n");
  6
  7    return 0;
  8 }
```

We can compile it like so in Linux `gcc hello_world.c -o hello_world` and run it:

```bash
$ ./hello_world
Hello World!
```

All this program really does is print "Hello World!" to the screen...but, we have to back up a little bit because in reality a lot happened when we compiled it.

The compiler we used is called `gcc` and what the compiler does, is translate the `C` code to machine code that the computer can run. After all the computer only knows 1 sand 0s, but it would be hard for us to program in all 1s and 0s to do a simple print "Hello World" to the screen. This is why we use high-level languages.

## Compilation Process

The compilation process actually has multiple steps/stages to get down to the executable code:

Pre-Processing → Compiling —>Assembling —> Linking —> executable\_file

## Pre-Processing

- At this stage comments are removed
- We trade in all the `#include` lines for the actual library/header code
    - The compiler search standard library paths for lines that include with `<>` angle brackets
    - Otherwise, the paths use the working directory of the source code
- Macros are replaced for the actual code

We can see what this step look like with gcc

`gcc -E hello_world.c -o hello_world.i`.

In my case this expanded my 8 lines of code into 1825! Snippet below:

```c
<< snipped >>
1816 # 1023 "/usr/include/stdlib.h" 3 4
1817
1818 # 3 "hello_world.c" 2
1819
1820 # 3 "hello_world.c"
1821 int main(void)
1822 {
1823    printf("Hello World!n");
1824    return 0;
1825 }
```

## Compiling

In this step we take the preprocessor output and generate assembly code. We can take a look at it by using `gcc -S hello_world.c` this will output the `hello_world.s` assembly code file.

For comparison, this file went down to 44 lines! Snippet:

```c
<< snipped >>
 41         .long    0x3
 42 3:
 43         .align 8
 44 4:
```

While it is 36 more lines than my original, it is a lot more compact than the preprocessor output, this is because the compiler is so smart that it gets rid of unnecessary/unused code and performs optimizations on the code needed.

## Assembly

In the Assembly stage it takes the assembly code from the compiling stage and translates it into low-level machine code/object code targeted at the architecture provided, that is, targeting the processor we intend to run the code on (Examples are Intel, ARM, MIPS, other...).

We can generate the object file with `gcc -c hello_world.c` and the output should be a `hello_world.o`. If you are interested in looking at the content you can try `hexdump -C hello_world.o` but it mostly looks like a bunch of garbage at this point. Snippet:

```c
00000000  7f 45 4c 46 02 01 01 00  00 00 00 00 00 00 00 00  |.ELF............|
00000010  01 00 3e 00 01 00 00 00  00 00 00 00 00 00 00 00  |..>.............|
00000020  00 00 00 00 00 00 00 00  18 03 00 00 00 00 00 00  |................|
00000030  00 00 00 00 40 00 00 00  00 00 40 00 0e 00 0d 00  |....@.....@.....|
<< snipped >>
```

## Linking

In this final step it takes the object code and any library functions and links our code calling said library functions with the library code.

We are going dig a little deeper and understand that there are two types of linking: Static Linking and Dynamic Linking.

Static linking includes the necessary code within the binary/executable file. This allows the binary to be more portable in that if they libraries are missing in a system where we are running it, it should still execute since the code is included within it. However, this makes the executable size bigger.

Dynamic linking allows the code to use the libraries in the system where the executable is going to run. This makes the executable size more compact since it doesn’t have to include all the other code, and it allows to “re use” code from the libraries that are included in the system. The flip side is, that the binary expects the library to be in the system where it will be running, thus if the library is missing the binary won’t run.

As you can see there is pros and cons of each type of linking!

The final product is our executable file.

# NEXT...

In my next post to this series, I am thinking of introducing some of the tooling that is useful so we can get our hands dirty and see where that takes us!

# Reference Book...

A book that I can recommend if you would like to dig into this a bit more is [Hacking: The Art of Exploitation, 2nd Edition](%5Bhttps://amzn.to/3tKHuRW%5D\(https://amzn.to/3tKHuRW\))

# Reference Sites:

[https://www.geeksforgeeks.org/compiling-a-c-program-behind-the-scenes/](https://www.geeksforgeeks.org/compiling-a-c-program-behind-the-scenes/) [https://en.wikipedia.org/wiki/Compiler](https://en.wikipedia.org/wiki/Compiler)
