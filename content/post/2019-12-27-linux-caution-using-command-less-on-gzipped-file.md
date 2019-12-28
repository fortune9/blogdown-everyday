---
title: '[Linux] Caution: using command ''less'' on gzipped file'
author: Zhenguo Zhang
date: '2019-12-27'
slug: linux-caution-using-command-less-on-gzipped-file
categories:
  - Tips
  - Computing
tags:
  - Linux
  - Programming
---

A couple of years ago, I found that I can use Linux command *less*
to view gzipped file, such as:

```bash
less in.gz
```

This is very convenient to view gzipped file content.

However, recently, I noticed a problem with it. For a file which
has 42196516 lines, if I opened it with *less*, it gave me a count
of 30356021 lines:

```bash
less test.fq.gz | wc -l; # giving 30356021
gzip -dc test.fq.gz | wc -l; # giving 42196516
```

Therefore, *gzip -dc* gives correct answer while *less* truncates
the input stream.

In conclusion, for safety, always use *gzip*, other than *less*,
to open gzipped files.
