---
title: "Master Boot Record (MBR) Research"
date: "2019-08-30"
coverImage: "MBR_dd.png"
---

I was given a virtual disk and asked to traverse through it and find all the partitions and list each partition's information which is located in the Partition Table aka Master Boot Record.

The challenge looked pretty straight forward, but I had to dig deeper in to MBRs to understand how to do this.

I used C for this assignment, it was a requirement and it is a good choice anyway when doing low level things like this.

First a little background, any storage device ssd, hdd, sd card, flash drive, etc.., is divided into sectors. Partitions are a collection of sectors. Your storage device can then be separated into different partitions to do things such as installing different operating systems, separating a disks in to different volumes.

In the first sector (sector 0), if you read in the first 512 bytes (assuming your sectors are size 512 in your case, adjust as needed) in to a buffer, and you traverse to the offset of 0x1BE and you will find the Partition Table aka MBR. This partition table contains 4 entries, this are the first 4 partitions.

The partition is setup in the following C struct that is 16 bytes:

```
struct partition {
 u8 drive;             /* drive number FD=0, HD=0x80, etc. */

 u8  head;             /* starting head */
 u8  sector;           /* starting sector */
 u8  cylinder;         /* starting cylinder */

 u8  sys_type;         /* partition type: NTFS, LINUX, etc. */

 u8  end_head;         /* end head */
 u8  end_sector;       /* end sector */
 u8  end_cylinder;     /* end cylinder */

 u32 start_sector;     /* starting sector counting from 0 */
 u32 nr_sectors;       /* number of of sectors in partition */
};
```

The rest of the partitions are located in a Local MBR, which is located in the first sector of the fourth partition. This Local MBR has 2 entries. The first of the two contains the information for that partition, the second entry the information for the next local MBR.

The following local MBRs are all in respect to the 4th Partition. This is important as you need to traverse through the disk relevant to Partition 4's start sector for any additional partitions.

The way to traverse through this as follows:

1. Read partition 4's (I will call it p4) first sector into a buffer
2. Go to the offset 0x1BE of that buffer
3. Assign that location to a struct partition \*p
4. Assign the p4->start\_sector to an p4\_offset variable
5. Use lseek to move to that offset, so offset\*512
6. Read that buffer again for 512 bytes
7. Assign p = &(buffer\[0x1BE\])
8. Increment the pointer to get to the second entry, p++
9. Access the struct via p->start\_sector and save to a temp\_offset variable
10. lseek again to the next local MBR using (p4\_offset+temp\_offset)\*512

So you can essentially traverse to the next MBR using a loop and I will eventually post the code online some time in the near future.

Note: I also ran into a weird issue. If I ran the code without a loop it would work right, and then when I put in a loop it would not work right. So I ended up having to modify the code a little bit. My guess is that this might have something to do with compiler optimizations and the fact that we are dealing with lower level stuff.

#### Resources:

[https://thestarman.pcministry.com/asm/mbr/PartTables2.htm](https://thestarman.pcministry.com/asm/mbr/PartTables2.htm)  
[https://thestarman.pcministry.com/asm/mbr/PartTables3.htm](https://thestarman.pcministry.com/asm/mbr/PartTables3.htm)  
WSU CptS360
