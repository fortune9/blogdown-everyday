---
title: '[Linux] How to preserve environment variables in `sudo`'
author: Zhenguo Zhang
date: '2021-06-09'
slug: linux-how-to-preserve-environment-variables-in-sudo
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

When one runs sudo, he actually starts a new environment
as the root user, so any environment variables in current
shell environment will not be transferred to the new root
user environment. 

To transfer current environment variables to the new
environment, one can run the following command:

```bash
curVar="variable in current environment"
sudo -E echo $curVar
# output: "variable in current environment"
```

Here the option `-E` lets the sudo to preserve user's
environment, so the variable `curVar` is visible in sudo.

However, the environment variable `PATH` is special, itself
will still be visible in the new environment, but will not
affect where sudo to find its command, even with
the `-E` option, because when sudo is called, it finds
a variable 'secure_path' in the file '/etc/sudoers' (for Ubuntu)
and use that value as the 'PATH' variable. The 'secure_path'
normally looks like this:

```
secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"
```

One can use `sudo visudo` to edit this file to add additional
directories for sudo to use. 

An alternative solution is below:

```bash
# assume there is a executable '/opt/bin/hello' 
PATH=/opt/bin:$PATH
sudo -E echo $PATH # you can see the PATH contains /opt/bin
sudo -E hello # but this fails
# a workout is
sudo env "PATH=$PATH" hello
```

The above command lets sudo calls `env`, and `env` creates
a new environment by setting 'PATH=\$PATH'. In the `env`
set environment, it runs the executable 'hello'. The trick
here is that 'hello' is run in `env`'s environment, not
sudo's environment.

Happy programming :smile:

## References

1. A stackexchange post on this issue: https://unix.stackexchange.com/questions/442747/why-do-the-following-ways-to-preserve-environment-variables-for-sudo-not-work

2. Another post: https://superuser.com/questions/709515/command-not-found-when-using-sudo
