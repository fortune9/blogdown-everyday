---
title: '[Python] Install quarto in Windows'
author: Zhenguo Zhang
date: '2025-11-08'
slug: python-install-quarto-in-windows
categories:
  - Computing
tags:
  - Python
  - R Markdown
  - quarto
description: ''
featured_image: ''
images: []
comment: yes
---

[Quarto](https://quarto.org/) is a popular tool for creating dynamic documents, 
reports, presentations, and websites using markdown and code. 
It supports multiple programming languages, including Python, R, and Julia.

When I installed it in my Windows, I found some challenges to make it find
correct python in different environments, so I would like to share my experience here to
help you.

## Installation

You can download the installer for Windows from the official Quarto website:
https://quarto.org/docs/get-started/

## Choose the correct python version

You may have multiple python versions installed in your system, such using
pyenv-win. When you run quarto commands, it may not find the correct python version
you want to use. Here are the solutions:

### Command line

set environment variable `QUARTO_PYTHON` to the python executable path you want to use.
For example, in PowerShell, you can run:

```powershell
$env:QUARTO_PYTHON="C:\path\to\your\python.exe"
```

### Rstudio

add the following into the .Rprofile file: 

```r
Sys.setenv(QUARTO_PYTHON = "D:/python/to/python.exe")
```

### VS Code

Use the command palette (Ctrl+Shift+P), search for "Python: Select Interpreter",
and choose the desired Python environment.

Note that if the .qmd document contains both R and python code chunks, then you may
also set another environment variable `RETICULATE_PYTHON` to the python executable path
for the above first two methods.

## Methods not working

I also tried the foollowing methods, but they did not work for me:

- add the following into `_quarto.yml`

  ```yaml
  execute:
    python: "C:/path/to/python.exe"
  ```
  
  This could be due to my folder where `_quarto.yml` is located was not a project folder.
  I will test this again in the future.

- set the python version using Rstudio global options settings

  When I tried to select the python version in Rstudio global options, it couldn't
  find the python versions installed by pyenv-win or by system.


## Related resources

- Quarto-surported formats: https://quarto.org/docs/reference/formats/html.html
- How quarto finds python in different environments: 
  https://quarto.org/docs/projects/virtual-environments.html


I hope that this post helps you get started with Quarto in Windows using Python!
Happy Quartoing 😊
