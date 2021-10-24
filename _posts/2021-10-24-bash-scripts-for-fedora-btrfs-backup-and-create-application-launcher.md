---
layout: post
title: "Bash scripts for Fedora: Btrfs backup & Create application launcher"
date: 2021-10-24 12:00:00 +0100
categories: ["bash"]
---

Two scripts for more convenient usage of Fedora (and possibly other linux distro):

## backup-btrfs:
Download [here](https://github.com/ikossaczky/bash-utils/blob/master/backup-btrfs).

Script for performing backup of one or more btrfs filesystem.

- one backup copy is created on a local machine
- second backup copy is sent to remote drive of choice (e.g. USB stick)
- a fixed number of most recent backups is kept, all older old backups are deleted both locally and on remote drive

Due to the usage of copy-on-write in btrfs, these backups are much faster and take much less memory than standard backups. Btrfs filesystem is needed on both local PC and remote drive

**USAGE**: `backup-btrfs local_backup_folder remote_backup_folder`

- by default 2 old bakcups backups are kept additionaly. If you want to keep a different number x of old backups, run with <br>
```KEEP_NUM=x backup-btrfs local_backup_folder remote_backup_folder```
- by default filesystems `/` and `/home` are backed up. To change this behaviour you can adapt the script (also, you may prefer to adapt the script to run with your default backup locations and without arguments).

## create-launcher
Download [here](https://github.com/ikossaczky/bash-utils/blob/master/create-launcher).

Script for creating a application launcher in application section of gnome shell and a executbale symlink in local bin directory. Also can be used to delete both again.

**USAGE**: `create-launcher [-d] name path-to-executable [path-to-icon]`

- creates symlink to executable with name "name" in $HOME/bin, so that calling the executable directly from shell by command "name" is possible
- creates launcher for gnome desktop. Uses the icon, if specified

**OPTIONS**:

- -d launcher and symlink with name "name" are deleted
- -h, --help: displays help
