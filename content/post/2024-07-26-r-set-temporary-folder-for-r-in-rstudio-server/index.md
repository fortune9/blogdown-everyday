---
title: '[R] Set temporary folder for R in Rstudio server'
author: Zhenguo Zhang
date: '2024-07-26'
slug: r-set-temporary-folder-for-r-in-rstudio-server
categories:
  - R
  - Computing
tags:
  - R
description: ''
featured_image: ''
images: []
comment: yes
---

In another [post](https://fortune9.netlify.app/2023/05/28/r-install-and-configure-rstudio-server-in-ubuntu/),
I described how to install Rstudio server on Ubuntu. In this post,
I will describe how to set temporary folders for R sessions connected
to an Rstudio server. This is important, because the default temporary
folder */tmp* could get overload easily when multiple R sessions are
connected and/or big data are stored, yielding lack of space issue.

## Temporary folder in R

One can get the temporary directory set for the current R session
by typing the following command in R terminal:

```r
tempdir()
```

The temporary directory was set by reading the following
environment variables in order until finding one pointing to a writable
directory:

- TMPDIR
- TMP
- TEMP 

If none is found, the temporary folder will be set to
*/tmp* in Linux.

So to change the tempoary folder of R sessions, one need to set one of these
variables, ideally *TMPDIR*.

## A bit introduction on R environment variable setting

When Rstudio launches R sessions, it does it under a bash login shell,
so before starting R, it reads configurations from the file
*/etc/profile* if it exists, and then first of the following:

- ~/.bash_profile
- ~/.bash_login
- ~/.profile

Therefore you can set up environment variables in any of these files.

Also, you can set environment variables in the following files:

- \${R_HOME}/etc/Renviron: affect all users
- ~/.Renviron: only impact the user whose home folder is changed

If you use Posit Workbench, you can also set environment variables in
the file */etc/rstudio/rsession-profile*, check
this [post](https://support.posit.co/hc/en-us/articles/12650492271127-Setting-the-temp-directory-in-Workbench). Note this won't work for free
Rstudio server.

## The solution

You can add the following line into the file
*~/.Renviron* to change temporary folders.

```bash
TMPDIR=/path/to/directory
```

You can also modify any of the other above-mentiond files to set the
environment variables.

Don't forget to restart R or Rstudio server for changes to take effect.

Then have a peace in data analysis, especially big data.


## References

- configuration of rsession.conf: https://docs.posit.co/ide/server-pro/reference/rsession_conf.html

- configuration of rserver.conf: https://docs.posit.co/ide/server-pro/reference/rserver_conf.html

- Rstudio server management: https://docs.posit.co/ide/server-pro/1.4.1722-1/server-management.html

- R session management: https://docs.posit.co/ide/server-pro/1.4.1722-1/r-sessions.html

