---
title: "rsync"
date: "2021-10-18"
tags: 
  - "cybersecurity"
  - "ethicalhacking"
  - "hacking"
  - "infosec"
  - "linux"
  - "lolbins"
  - "offensivesec"
  - "offensivesecurity"
  - "pentesting"
  - "redteam"
  - "rsync"
  - "security"
  - "sync"
  - "synchronization"
---

`rsync` is a powerful little tool that is pre-installed in a lot of distros that we can use to synchronize files/directories in Linux.

This example essentially makes copies/updates the files in `/path/to/destinationDir` to be the same as `mySourceDir`:

```sh
rsync -avuP mySourceDir /path/to/destinationDir
```

'rsync' can leverage SSH to keep files in sync in remote machines. Once you have setup your keys and connections can be established, it is pretty simple:

```sh
rsync -avuP myLocalDir remoteUser@remoteMachine:/path/to/remote/dir
```

We can leverage other utilities to do this automatically for us, such as `cron` jobs, `inotify` (does require installing `inotify-tools`), and scripts.

### Reference:

- https://www.redhat.com/sysadmin/sync-rsync
- https://stackoverflow.com/questions/12460279/how-to-keep-two-folders-automatically-synchronized
- https://unix.stackexchange.com/questions/203846/how-to-sync-two-folders-with-command-line-tools
- `man rsync`
- https://goinglinux.com/articles/Rsync-Bash-Cron-Backups\_en.htm
- https://unix.stackexchange.com/questions/392780/how-to-schedule-an-rsync-command
- https://stackoverflow.com/questions/14073389/rsync-code-will-run-but-not-in-cron
- https://www.jveweb.net/en/archives/2011/02/using-rsync-and-cron-to-automate-incremental-backups.html#jveweb\_en\_027\_05
