---
title: "Installation of Atom on Windows 10"
author: "Zhenguo Zhang"
date: '2021-04-07'
slug: installation-of-atom-on-windows-10
categories:
- Tips
- Computing
- Software
tags: Windows
description: ''
featured_image: ''
images: []
comment: yes
---


[Atom](https://atom.io/) is a very cool editor built 
with HTML, JavaScript, CSS, and Node.js. Due to this,
one can customize Atom with the web tools. Atom runs on Electron,
a framework for building cross platform apps using web
technologies.

Today, I just installed Atom, and here I would like to
share some tips on the process.

## Installation

There are two ways to install Atom version 1.55.0.

1. Using exe file: Download [the exe file](https://github.com/atom/atom/releases/download/v1.55.0/AtomSetup-x64.exe),
and then double click it to install as usual. With this option, users have
no option to decide where to install, and it will install to
*C:\Users\zhang\AppData\Local* by default.

2. Using zip file: Download [the zip file](https://github.com/atom/atom/releases/download/v1.55.0/atom-x64-windows.zip), and then extract it to a folder where
you want to install it, for example, *D:\tools\Atom*, and
it will lead to two subfolders: Atom and app. Then at a
command line enter the following command to install it:

```bash
cd D:\\tools\\Atom\\Atom
./atom.exe --squirrel-install 
```
In this way, it will create a new folder *D:\tools\Atom\bin*
and add it to the *PATH* environment variable. That's it.

## Packages

Again, there are two ways to install new packages.

One is through the command line, such as:

```bash
apm install atom-html-preview
```

The other way is through the GUI, clicking the following
buttons in order:

*File* >> *Settings* >> *Install*.

Then search the desired package at the top and install
selected packages.

In default, packages will be installed to user's profile
directory *C:\Users\zhang\.atom\packages*. To change this,
one can set a environment variable *ATOM_HOME*. At command
line, it is achievable by the following:

```powershell
setx ATOM_HOME D:\Tools\Atom\.atom
```

Some useful packages:

1. atom-html-preview: preview html files in atom.

2. platformio-ide-terminal: integrate external terminals
   (git-bash, powershell) to atom.
   
## Configuration

One can configure Atom under 'Settings', such as changing themes.

For each installed package, it can also be configured by clicking
'Settings' followed by choosing desired options.


