---
title: '[Thunderbird] Error: User is authenticated but not connected'
author: Zhenguo Zhang
date: '2023-01-02'
slug: thunderbird-error-user-is-authenticated-but-not-connected
categories:
  - Software
  - Tips
tags:
  - Thunderbird
  - IMAP
  - Computer
description: ''
featured_image: ''
images: []
comment: yes
---

## Issue

In last few days, my thunderbird striked: it frequently popped two error
messages: "The service outlook.office365.com is disconnected" and "User
is authenticated but not connected".

## Research

After searching the website for a few days, I came to an solution, which
was found [here](https://answers.microsoft.com/en-us/outlook_com/forum/all/thunderbird-imap-responded-user-is-authenticated/062a82f6-e678-4462-88b7-dd6cc318386f).

## Solution

Go to *Settings -> Config Editor*, and then search
*network.dns.disableIPv6* and click the toggle switch
on the right to make it 'true'. Finally close and
reopen Thurderbird, and the problem should be resolved.

Happy New Year! :smile:

