---
title: '[Tutorial] How to install Shiny server on Ubuntu 22.04'
author: Zhenguo Zhang
date: '2023-06-30'
slug: tutorial-how-to-install-shiny-server-on-ubuntu-22-04
categories:
  - R
  - Computing
  - Tips
tags:
  - AWS
  - R
  - Shiny
description: ''
featured_image: ''
images: []
comment: yes
---

R shiny is a great tool for one to present research results in a dynamic and
interactive way. Today, I'd introduce how to install R shiny server on Ubuntu
22.04. The content will include installation, configuration, and connecting to
R shiny server.

## Installation

Here I install the compiled version by downloading from the 
[R shiny server website](https://posit.co/download/shiny-server/).
As of my writing, the Ubuntu version supports Ubuntu 18.04 and above.
If no compiled version is available, one can compile it by following the
instruction [here](https://github.com/rstudio/shiny-server/wiki/Building-Shiny-Server-from-Source).

### Install dependencies

```bash
sudo apt-get update
sudo apt -y install libcurl4-openssl-dev
sudo apt -y install libssl-dev libxml2-dev libmariadbclient-dev build-essential libcurl4-gnutls-dev
sudo su - -c "R -e \"install.packages('shiny', repos='https://cran.rstudio.com/')\""
```

### Install Shiny Server

```bash
sudo apt-get install gdebi-core
wget https://download3.rstudio.org/ubuntu-18.04/x86_64/shiny-server-1.5.20.1002-amd64.deb
sudo gdebi shiny-server-1.5.20.1002-amd64.deb
```

The installation will install R shiny server as a service so it can be started, stopped
using the following commands like:

```bash
sudo systemctl start shiny-server
sudo systemctl stop shiny-server
sudo systemctl restart shiny-server
sudo systemctl status shiny-server
```

Also, if it installed successfully, one should be able to access the server
by inputting the following url:

> http://server-ip-address:3838


## Configuration

For complete configuration, please refer to this [document](https://docs.posit.co/shiny-server/).
Here I will mention how to configure the path to R if it is installed into a customized postion.

In my case, the R was installed at /opt/R/4.3/bin/R, so when visiting the shiny server,
you would probably see the errors showing initialization failed, and the cause could
be further found by checking the log files at /var/log/shiny-server/.

To fix this, there are two ways:

- For Pro version of R shiny server, one can use the variable `r_path=/path/to/R`
  to set the R used by shiny server in /etc/shiny-server/shiny-server.conf.

- For open-source community version, which was the version I installed, one can do
  the following:
  
  ```
  /home/shiny/bin # make sure /home/shiny/.profile includes this in the PATH
  sudo ln -s /opt/R/4.3/bin/R /home/shiny/bin/R # create a symbol link
  ```
  
  This will creates a symbolic link to R, and the folder with the symbolic link
  is in the list of `$PATH`.
  Then re-access the server page should resolve the failed initialization errors.
  Note that the above settings assume in the server configuration, `run_as shiny`
  is set, and this is default. If not, one need to change the home folder to
  edit corresponding files.
  
  Note that some posts said to set environment variable 'R=/path/to/R' in
  /home/shiny/.profile, and I tested it and it did not work.


## Connect to server

As described above, one can access the shiny server by inputting

> http://server-ip-address:3838

in a browser. One can also access each sample apps by inputting

> http://server-ip-address:3838/sample-apps

To add one's own shiny apps (e.g., my-app) to the server, one can copy them
to the folder /srv/shiny-server/, restart the sever, and access it using the following
address:

> http://server-ip-address:3838/my-app/


Happy programming :smile: !

## References

- Install from source: https://github.com/rstudio/shiny-server/wiki/Building-Shiny-Server-from-Source

- Tutorial of installing R shiny server on Ubuntu 18: https://www.john-mcallister.com/deploy-rstudio-and-shiny-server-on-ubuntu/

- Shiny server configuration: https://docs.posit.co/shiny-server/
