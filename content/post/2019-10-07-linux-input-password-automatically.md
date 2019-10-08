---
title: '[Linux] Input password automatically'
author: Zhenguo Zhang
date: '2019-10-07'
slug: linux-input-password-automatically
categories:
  - Computing
  - Tips
tags:
  - Linux
---

In this post, I will introduce an approach to input password
programatically, which is useful if a repeated command needs
password.

**Note: this approach is subject to the risk of exposing
your passwords to others, so use it with caution.**

Let's use *sudo* as an example. Normally when you type

```bash
sudo ls
```

It will prompt for a password if you set one.

However, if you type

```bash
sshpass 'your-password' sudo ls
```

Then the passord is fed into the command directly. Here the command
*sshpass* is from a package ___sshpass___, which can be installed
with

```bash
sudo apt-get install sshpass
```

Hope that this tip helps.
