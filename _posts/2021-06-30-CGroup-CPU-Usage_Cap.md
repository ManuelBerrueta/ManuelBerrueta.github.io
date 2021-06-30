# Using Linux CGroups to Cap CPU Usage 

There may come a time in your time where you may want to limit the amount of CPU a process/program uses on a machine for a number of different reasons. I found the best way to do it is using CGroups. You can dive and get into the weeds of CGroups here: [man cgroups](https://man7.org/linux/man-pages/man7/cgroups.7.html). As a side note/bonus, containers use CGroups to help provides the container(s) (e.g.
 Docker/containerd) that isolation.

For my testing purposes, I wanted to limit the CPU usage to 5%. We can do this by using the cpu.cfs_quota_us and cpu.cfgs_period_us properties of the CGroup. To provide the correct ratio you just do a bit of basic math to bring you to the target usage percentage. What it comes down to here is the following: `cpu.cfs_quota_us / cpu.cfgs_period_us = CPU Usage Percent Cap`.

Thus for my purposes I will be using the following values
```
cpu.cfs_quota_us =   10000
cpu.cfgs_period_us = 200000
```
To verify, 10000 / 200000 = 0.05 == 5%, my target CPU usage cap.

## Setting up the CGroup

**Pre-requisite**
Install the bins to help us work with CGroups (Ubuntu/Deb):   
`sudo apt install cgroup-tools`   
For Red Hat, Fedora use dnf/yum; Arch use Pacman, etc...   

First we create our new CGroup
 `sudo cgcreate -g cpu:cpulimited`

Let's set the cpu.cfs_quota_us and cpu.cfgs_period_us properties
```
sudo cgset -r cpu:cpulimited cpu.cfs_quota_us = 10000
sudo cgset -r cpu:cpulimited cpu.cfgs_period_us = 200000
```
Altenatively, you could also use this method if you run into issues:
```
echo 10000 > /sys/fs/cgroup/cpu/cpulimited/cpu.cfs_quota_us
echo 200000 > /sys/fs/cgroup/cpu/cpulimited/cpu.cfs_period_us
```
**NOTE: Make sure the path is correct**

Let's check the properties that we have given our CGroup to inspect what you expect!
`sudo cgget -g cpu:cpulimited`

Now we can run our program/script like so:
`sudo cgexec -g cpu:limited ./your_program_or_script.sh`

If you are going through all this trouble, you might also be interested in the execution time. You can get that by using the time utility like so:
`time sudo cgexec -g cpu:limited ./your_program_or_script.sh`
