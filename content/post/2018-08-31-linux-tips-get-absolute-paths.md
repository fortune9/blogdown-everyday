---
title: '[Linux Tips] get absolute paths'
author: Zhenguo Zhang
date: '2018-08-31'
slug: linux-tips-get-absolute-paths
categories:
  - Computing
  - Tips
tags:
  - Linux
---

In my research, I frequently fell into occasions when I want to solve
some tiny tasks or to solve tasks more efficiently. Facing the situations,
the first thing I did was google search. However, from today on, I will
record these solutions in my blog: for one thing, I can revisit them when
I need them in future (very likely), and for another, others may also
benefit from the compiled list of tips.

So, let's start.

Today, I would like to share how to get the absolute path from a relative
path. There are two commands for the purpose: *realpath* and *readlink*.
One example is shown below:

```
readlink -f ../../folder1/file1
realpath ../../folder1/file1
```

The above commands will return absolute path, like the format /home/ec2-user/folder1/file1.
Note, if the provided file is a symlink, then the path to the
target file (the symlink points to) is returned.

Done!! :smiley:

## References

1. realpath: https://linux.die.net/man/1/realpath
2. readlink: https://linux.die.net/man/1/readlink