---
title: '[Linux] How to escape single quote in ''sh -c'''
author: Zhenguo Zhang
date: '2025-05-04'
slug: linux-how-to-escape-single-quote-in-sh-c
categories:
  - Computing
tags:
  - Linux
  - Bash
description: ''
featured_image: ''
images: []
comment: yes
---

Sometimes, you need to run a command in 'sh -c' and to avoid its subshell
expression is evaluated in parent shell, you need to put the command string
into a single quote, like:

```bash
sh -c 'a="hello world"; echo $a'
```

However, for some commands in the command string, you need to use single quote
such as `awk`, and how do you escape the single quote in the command string?

If you use a backslash to escape the single quote as we do normally in bash,
it will look like this:

```bash
sh -c 'a="hello world"; echo $a | awk \'{print $2}\' '
```

It will not work, and show errors like:

```markdown
awk: cmd. line:1: {print
awk: cmd. line:1:       ^ unexpected newline or end of string
```

The **correct way** to escape the single quote in the command string is to use
3 single quotes, like this:

```bash
sh -c 'a="hello world"; echo $a | awk '\''{print $2}'\'''
```

This will work as expected and print `world`.

So that's it. Use `'\''` to escape a single quote in the command string of `sh -c`.

Happy Programming. :smiley:





