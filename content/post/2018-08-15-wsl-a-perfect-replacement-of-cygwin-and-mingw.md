---
title: WSL -- a perfect replacement of Cygwin and Mingw
author: Zhenguo Zhang
date: '2018-08-15'
slug: wsl-a-perfect-replacement-of-cygwin-and-mingw
categories:
  - Computing
tags:
  - Linux
  - OS
  - Windows
---

As a program developer, I love working on the command of Linux for its richment in convenient tools, such as sed, awk, grep, vi, etc.

Every time when I bought a new windows computer, I would
install [cygwin](https://www.cygwin.com/) or [MinGW](http://www.mingw.org/)
for using these linux tools. With windows 10 (version  Fall Creators update or later),
one can install Windows Subsystem for Linux (WSL) in windows 10 from
store, just like installing an app. 

Available WSL systems are:

* [Ubuntu](https://www.microsoft.com/store/p/ubuntu/9nblggh4msv6)
* [Debian](https://www.microsoft.com/store/apps/9MSVKQC78PK6)
* [OpenSUSE](https://www.microsoft.com/store/apps/9njvjts82tjx)
* [SLES](https://www.microsoft.com/store/apps/9p32mwbh6cns)
* [Kali Linux](https://www.microsoft.com/store/apps/9PKR34TNCV07)

Different from Cygwin or MinGW, all the programs under WSL can be natively compiled in the Linux environment. For more
information on what a WSL is, check the [link](https://blogs.msdn.microsoft.com/commandline/learn-about-windows-console-and-windows-subsystem-for-linux-wsl/).

For how to install WSL, check this [link](https://docs.microsoft.com/en-us/windows/wsl/install-win10). Particually, you need enable an option in Windows PowerShell **as an administrator** to install successfully, input the following command in the PowerShell terminal and restart by following the instruction:

```
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

Enjoy Linux under Windows. :smiley:
