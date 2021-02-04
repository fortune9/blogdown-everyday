---
title: '[Tips] Get the OS version of Linux'
author: Zhenguo Zhang
date: '2019-11-02'
slug: tips-get-the-os-version-of-linux
categories:
  - Tips
  - Computing
tags:
  - Linux
  - OS
---

There are many variants of Unix/Linux systems, such as Ubuntu,
Redhat, and even some versions in Windows system such as
MinGW/Msys and Cygwin.

Today I am going to introduce a way to get such information, which is
simple, run the following command

```bash
uname -o
```

Running this command in Ubuntu and Redhat will yield __GNU/Linux__,
while running it under MinGW/Msys yields __Msys__. If you run

```bash
uname -r
```

will give you kernel release.

Actually, the command *uname* can output more information than this,
if you add the option '-a', including kernel name, hostname, and
processor type. Check the command's manpage for more information.

## Other methods

In addition to *uname*, one can also use the following command to
output OS information:

```bash
lsb_release -a
cat /proc/version
cat /etc/os-release
```

Happy computing. :smile:
