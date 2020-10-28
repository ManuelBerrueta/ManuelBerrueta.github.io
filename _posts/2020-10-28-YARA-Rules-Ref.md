---
layout: post
title: YARA Rules Reference
date: 2020-10-28
category: Security
tags: [YARA,ThreatHunting,Dectection,Security]
---
#YARA Rules Reference

This is just a simple post for reference as well as to show the anatomy of a YARA rule.

```YARA
/* * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
 *  Dissected YARA Rule Example                                              *
 *                                                                           *
 *  The "import" keyword allows us to use modules to extend YARA's core      *
 *  functionality                                                            *
 *  Every yara rule, start with the rule keyword.                            *
 *  Evil_Bin is the name of the rule                                         *
 *  BadBins & Malware are tags, there is no limit. Can be used to filter     *
 * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * */

import "pe"

rule Evil_Bin : BadBin Malware
{
        meta: // Metadata about the rule. Does not affect how the rule works!
            author      = "Revx0r @ManuelBerrueta"
            description = "Testing Yara rules"
            created     = "10/28/2020"

        strings:
            $evil = "evil.c2.com" // Sample string to be used in condition
            $evil2 = "veryevil.c2.com" // Second string to be checked
            $pe = { 4d 5a } // Portable Executable (PE) Magic Number

        condition:
            ($evil1 or $evil2) and $pe in (0..2) and pe.entry_point == 0x1000
        /* * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
         * If either evil or evil2 strings match AND the first 2 bytes of    *
         * the file are 4d 5a, AND the entry point is at 0x1000. It matches  *
         * the rule and triggers!                                            *
         * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * */
}
```
<script src="https://gist.github.com/ManuelBerrueta/26ad0e52b771de8cc5069603d68c95fc.js"></script>
###Gist Link:
[YARA_Rule_Exampel.yar](https://gist.github.com/ManuelBerrueta/26ad0e52b771de8cc5069603d68c95fc)

##Resources:
- [YARA Official Docs](https://virustotal.github.io/yara/)
- [YARA Rules Overview](https://cybersecurity.att.com/blogs/security-essentials/explain-yara-rules-to-me)
