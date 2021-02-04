---
title: '[Linux] Find all filenames pointing to the same file'
author: Zhenguo Zhang
date: '2018-10-25'
slug: linux-find-all-filenames-pointing-to-the-same-file
categories:
  - Tips
tags:
  - Linux
---

In Linux/Unix systems, the same file can have multiple copies with
different names, termed hardlinks. This provides convenience of modifying
all the copies by modifying any copy. Today, I will introduce how to find
all the hardlinks for a file.

## How many copies does a file have?

One can find this information by using the following command:

```bash
ls -l sample-file.txt
# output
# -rwxrw-r--. 2 user hacker 3351 Oct 23 11:11 sample-file.txt
```

The above output indicates that the file *sample-file.txt* has two
copies: the second field is '2' (if it was 1, then this file would
be the only copy).

## Find all the copies in the system

To find all copies for this file, we have two options, as follows:

```bash
# option 1:
sudo find / -inum 656361
# option 2:
sudo find / -samefile sample-file.txt
```

For the above option 1, I used inode number 656361, which is the unique
number for each file in a system; actually all file copies/hardlinks
share the same inode number.

To find the inode number for a file, one can use the following command:

```bash
ls -i sample-file.txt
# output
# 656361 sample-file.txt
```

If one doesn't want to search the entire system but a folder,
the path '/' can be replaced with the folder path in the above methods.

I hope that this introduction helps. Happy programming :smiley:.

## References

1. inode introduction: http://teaching.idallen.com/dat2330/04f/notes/links_and_inodes.html