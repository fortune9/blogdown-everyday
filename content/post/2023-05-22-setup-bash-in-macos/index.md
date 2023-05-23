---
title: Setup bash in MacOS
author: Zhenguo Zhang
date: '2023-05-22'
slug: setup-bash-in-macos
categories:
  - Computing
  - Software
tags:
  - Computer
  - Programming
  - Bash
description: ''
featured_image: ''
images: []
comment: yes
---

I have used [Bash](https://www.gnu.org/software/bash/) in my career for many years
and it is so convenient for data analysis.

In MacOS, however, the default shell is `zsh`. To make use of `bash` in MacOS,
one need the following steps:

## Install the latest version of bash

One can use [`brew` (the MacOS package manager)](https://docs.brew.sh/Installation) to install the latest bash.
This will install bash to `/usr/local/bin/bash`.

```bash
brew install bash
```

## Change the default shell to /usr/local/bin/bash

To change the default shell in MacOS, one need run the following
command:

```bash
# add the newly installed bash to the system list
sudo echo '/usr/local/bin/bash' >>/etc/shells
# change the default shell
chsh -s /usr/local/bin/bash
```

Now exit the terminal and reopen one, the default shell
should be the new bash.

## Setup .bashrc

Since the MacOS terminal uses login based shell, it won't
read the file `.bashrc` (in contrast to *nix systems).

Also, the bash shell reads the config file firstly found in the
following order (the rest are ignored:

```
1. ~/.bash_profile
2. ~/.bash_login
3. ~/.profile
```

So one need to include the following section in the config file to
trigger `.bashrc`.

```
if [[ -r ~/.bashrc ]]; then
  source ~/.bashrc
fi
```

## Install coretutils package

[coreutils](https://www.gnu.org/software/coreutils/) contains the main programs
of GNU operating systems, including `dircolors`. Therefore, to use the tools,
one can install it with brew

```bash
brew install coreutils
```

This will install the utilities to a specific folder. To run these commands without
the folder prefix, one need to add this folder to `$PATH`.

For `dircolors`, one also need to change the path in `.bashrc` to take effect.

Happy programming :smile:!!!
