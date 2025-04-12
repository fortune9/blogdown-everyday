---
title: '[Linux] How to set limit on memory usage in a shell?'
author: Zhenguo Zhang
date: '2025-04-12'
slug: linux-how-to-set-limit-on-memory-usage-in-a-shell
categories:
  - Computing
  - Tips
tags:
  - Linux
description: ''
featured_image: ''
images: []
comment: yes
---

Occasionally, my computer crashed when I ran a program which ate up
all the memory. To prevent this from happening again, I want to set a limit on
the memory usage in a shell, so any command run in that shell will be limited
to set memory usage.

## My system

- OS: Ubuntu 22.04


## Solution not working

I searched Google and many suggested to use

```bash
ulimit -m <value>
```

to set memory usage, but it did not work -- no restriction on memory at all.

## Solution

I eventually went with the following solution:

```bash
ulimit -v <value>
# example for 40KB
ulimit -v 40
```

This command sets the maximum virtual memory size for the shell, and it worked.

The command `ulimit -m` is supposed to limit the Resident Memory Size (RSS), and
the command `ulimit -v` is supposed to limit the Virtual Memory Size (VMS), which
includes RSS and swapped/shared memories. Therefore, the command `ulimit -v` is more
stringent for the total memory usage.


## Another solution

One may also try to use the following command, but I have not tried it yet:

```bash
systemd-run --scope --user -p MemoryMax=250M -- /path/to/program/to/use
```

Happy Programming :smile:.
