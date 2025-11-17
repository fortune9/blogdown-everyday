---
title: '[VS code] How to use Google Colab in VS Code'
author: Zhenguo Zhang
date: '2025-11-16'
slug: vs-code-how-to-use-google-colab-in-vs-code
categories:
  - Computing
  - Software
tags:
  - Resource
  - Tools
description: ''
featured_image: ''
images: []
comment: yes
---


Recently I found that I can use Google Colab in VS Code, which is very convenient.
In this post, I will show how to do it.


## Install Google Colab extension

First, you need to install the [Google Colab extension for VS Code](https://marketplace.visualstudio.com/items?itemName=Google.colab).

You can find the extension by searching the Extensions view in VS Code for "Google Colab" and then install it, as below:

![Colab extension screenshot](/post/images/05-extension-Screenshot-2025-11-10-174057.png)

## Collect to Colab

Restart VS Code after installing the extension, and then open a Jupyter notebook file (`.ipynb`).
And try to select a kernel by clicking the kernel name in the top right corner, as below:

![Select kernel screenshot](/post/images/00-select-kernel-Screenshot-2025-11-10-173138.png)

Next choose 'Colab' and selet 'Auto Connect' or choose to create a new GPU/TPU Colab server as
shown in the screenshot:

![Connect to Colab screenshot](/post/images/01-select-colab-Screenshot-2025-11-10-173309.png)


And you will be prompted to sign in using Google, choose *Allow* to proceed, and you
will see following screens:

![Colab sign in screenshot](/post/images/03-colab-permission-Screenshot-2025-11-10-173516.png)

Follow the instructions and continue, and you will be connected to Colab server.
Next, you can start run your code as in Colab website.

Congratulations! You have successfully connected to Google Colab in VS Code.
:smile: :muscle:

