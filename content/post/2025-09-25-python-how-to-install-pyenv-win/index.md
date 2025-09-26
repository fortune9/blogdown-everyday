---
title: '[Python] How to install pyenv-win?'
author: Zhenguo Zhang
date: '2025-09-25'
slug: python-how-to-install-pyenv-win
categories:
  - Software
tags:
  - Python
  - Windows
  - Programming
description: ''
featured_image: ''
images: []
comment: yes
---

If you have used [pyenv](https://github.com/pyenv/pyenv) on Linux or Mac, 
you may want to use [pyenv-win](https://github.com/pyenv-win/pyenv-win) on Windows.

Basically, these tools allow one to easily switch between multiple versions of Python.

There is already a good document for how to install pyenv-win in windows
at https://github.com/pyenv-win/pyenv-win?tab=readme-ov-file#installation. In
this post, I would like to share my experience and share some tips/tricks
you may need in your setup.

## Installation

I started with the git method, so what I did were as follows:

- open git bash or power shell

- run the following command to download pyenv-win

  ```bash
  git clone https://github.com/pyenv-win/pyenv-win.git $HOME/.pyenv"
  ```
- set environment variables in **PowerShell**

  ```powershell
  [System.Environment]::SetEnvironmentVariable('PYENV',$env:USERPROFILE + "\.pyenv\pyenv-win\","User")
  
  [System.Environment]::SetEnvironmentVariable('PYENV_ROOT',$env:USERPROFILE + "\.pyenv\pyenv-win\","User")
  
  [System.Environment]::SetEnvironmentVariable('PYENV_HOME',$env:USERPROFILE + "\.pyenv\pyenv-win\","User")
  
  [System.Environment]::SetEnvironmentVariable('path', $env:USERPROFILE + "\.pyenv\pyenv-win\bin;" + $env:USERPROFILE + "\.pyenv\pyenv-win\shims;" + [System.Environment]::GetEnvironmentVariable('path', "User"),"User")
  ```
- set PATH variable in **git bash**, **optional** if one doesn't use git bash

  ```bash
  echo 'export PATH="$HOME/.pyenv/pyenv-win/shims:$PATH"' >> ~/.bash_profile
  echo 'export PATH="$HOME/.pyenv/pyenv-win/bin:$PATH"' >> ~/.bash_profile
  ```

Now one can test the installation by running

```bash
pyenv --version
```

If you see the version number, then the installation is successful.

## Tips and Tricks

- What if you want to install pyenv-win to a different folder?

  For this, one need to create a symbolic link to the default folder
  `%USERPROFILE%\.pyenv`. For example, let's say I git clone pyenv-win
  to "D:\path\to\pyenv-win", then I can use the following command in windows
  **Command Prompt** (not PowerShell):
  
  ```cmd
  mklink /D "%USERPROFILE%\.pyenv" "D:\path\to\pyenv-win"
  ```
  
  Note that you may need to open the Command Prompt as an administrator.


- How to add existing installed pythons to pyenv?

  If you already installed some python versions outside pyenv (those not
  installed by using `pyenv install`), you can add them to the management
  of pyenv as follows:
  
  first create a symbolic link in the *versions* folder using **Command Prompt**:
  
  ```cmd
  mklink /D "%USERPROFILE%\.pyenv\pyenv-win\versions\3.13-system" "D:\Tools\Python313"
  ```
  
  Replace the string *D:\Tools\Python313* with your own path where a python
  folder stays and replace *3.13-system* with any string you want to refer to
  this version in pyenv
  
  Then run
  
  ```bash
  pyenv rehash
  ```
  
  to update the shims.

- What is wrong if I got the error: pyenv not found?

  This is very likely due to
  + the environment variables PYENV, PYENV_HOME and PYENV_ROOT
    are not correctly set; these variables should point to $HOME/.pyenv/pyenv-win,
    even when you install pyenv-win to a different folder and linked via
    symbolic link as described above.
  + the PATH variable don't contain the following paths:
    $HOME/.pyenv/pyenv-win/bin and $HOME/.pyenv/pyenv-win/bin/shims
  
  Make sure these variables are in the **user's environment space**, not in
  Systems environment.

- The specified python version using pyenv doesn't take effect
  
  Let's say one run
  
  ```bash
  pyenv local 3.13
  ```
  
  But the following command shows a different version
  
  ```bash
  python --version
  ```
  
  This is usually due to that the following folders are superceded by other python
  folders in the PATH:
  
  - $HOME/.pyenv/pyenv-win/bin
  - $HOME/.pyenv/pyenv-win/bin/shims
  
  So to resolve the issue, try to give higher priority to these folders by moving
  them to the front of the folder list in PATH, or delete the folders where pythons
  are installed.

When refer to paths in the post, I sometimes used `$HOME` to refer to
`%USERPROFILE%`. So depends on your terminal, you may want use the other instead.

I hope that this will help you to install pyenv-win and welcome to comment if you
find something new.

Happy programming :smile:

