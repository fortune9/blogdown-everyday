---
title: '[Linux] "NO EFI System Partition was found" when installing Ubuntu 22.04'
author: Zhenguo Zhang
date: '2024-12-29'
slug: linux-no-efi-system-partition-was-found-when-installing-ubuntu-22-04
categories:
  - Computing
tags:
  - Linux
description: ''
featured_image: ''
images: []
comment: yes
---

## Background

I have an old Lenovo Ideapad U410 machine, which has an updated Windows 10
system (original was Windows 7).

Given that the system is so slow after a decade, I decide to install Ubuntu
onto it and make it dual-boot.

So I created a bootable USB disk with Ubuntu 22.04 installation media on it
and start to install it by following the [tutorial](https://ubuntu.com/tutorials/install-ubuntu-desktop#1-overview).


## The problem

When I chose a free disk partition to install Ubuntu, it first popped a message
**No root file system defined**. For this issue, one just need to create a ext4
filesystem on this partition and make it mount at '/'. Also see
[this video](https://www.youtube.com/watch?v=wh0AztSCTBk).

The next issue after continuing the installation was popping up the
error "No EFI System was found", which was due to an old BIOS system.
Just like the image below:

![No EFI System](/post/images/no-efi-system.png)


## Solutions

Initially I tried to follow [this video](https://www.youtube.com/watch?v=xBps2dtcafE) to create a separate EFI
partition, but it led to a new problem at the end: grub could not
be installed to /dev/sda1, leaving the machine not bootable.

Finally, I just ignored the warning of "No EFI System was found",
and deleted the EFI partition created in the solution in the previous paragraph,
and continued installation. The installation was successful and it can be booted
from a Grub boot menu.


## References

- A discussion on the issue: https://askubuntu.com/questions/1144552/dual-boot-no-efi-system-partition-was-found


