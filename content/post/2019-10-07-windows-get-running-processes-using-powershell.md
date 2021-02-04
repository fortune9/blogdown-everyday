---
title: '[Windows] Get running processes using PowerShell'
author: Zhenguo Zhang
date: '2019-10-07'
slug: windows-get-running-processes-using-powershell
categories:
  - Computing
  - Tips
tags:
  - Windows
---

As a computational biologist, I may want to get the commands running in
current system. This is pretty simple to achieve in Unix-like systems
(using *ps -ef* or *top* command),
but need some effort in _Windows_ system. Today, I'd like to introduce
two ways to get the running processes in using Windows _PowerShell_.

The first is *get-process*, which will list running processes, generating
a table like the below:

Handles | NPM(K)  |  PM(K)   |   WS(K)  |   CPU(s) |    Id | SI | ProcessName          
--- | ---  |  ---   |   ---  |  --- | --- | --- | ---          
    162 |     12  |   1660   |    5160  |     0.14 |  8936 |  4 | acrotray             
   1191 |     99  | 105260   |   31800  |    79.52 |  1896 |  4 | Adobe Desktop Service
    244 |     15  |   3544   |    5432  |     7.00 | 15284 |  4 | AdobeIPCBro


To filter certain processes using a pattern match, one can use *findstr*
or *where-object* as below:

```
get-process | findstr "host"
# or use powershell's where-object command
get-process | where-object {$_.ProcessName -like "*host*"}
```

note the second command may output a longer list than the first,
and here $_ represents each input object (a process here), and
ProcessName is a member element (the full list of members can
be obtained by running *get-process | get-member*).


As you note above, the output only contains the running executable,
but no parameters. To get the full command, one need another command
*gwmi* (alias of *Get-WmiObject*) as shown below:

```
# to get all the commands
gwmi Win32_process | select commandLine
# to filter certain processes
gwmi Win32_process | select commandLine | where-object {$_ -like "*host*"} | format-list
```

The above command *select* is selecting a certain member/field and
*format-list* have the output as list (otherwise long commands will
be truncated).

I have this post for my future reference and hope it helps others.

Fun programming.

