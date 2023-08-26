---
title: '[R] install and configure Rstudio-server in ubuntu'
author: Zhenguo Zhang
date: '2023-05-28'
slug: r-install-and-configure-rstudio-server-in-ubuntu
categories:
  - R
  - Software
  - Computing
tags:
  - R
  - Linux
description: ''
featured_image: ''
images: []
comment: yes
---

[Rstudio server](https://posit.co/download/rstudio-server/) provides an integrative
environment for a data scientist to do data analysis in R and python and allows one
to login into it via a browser anywhere. Today, I will going to show how to install
Rstudio server in Ubuntu under AWS.

## Setup EC2 instance

To access Rstudio server in an EC2 instance, one need to open correct port in the
security group and set the *Source* to "Anywhere" (i.e., 0.0.0.0). The needed ports
are as follows:

- SSH: port 22
- HTTP: port 80
- Shiny: port 3838 # this is optional unless shiny server is needed
- RStudio: port 8787

## Install R

The next step is to install R in the EC2 instance after ssh to it.
Use the following command to install the R

```bash
# update indices
sudo apt update -qq
# install two helper packages we need
sudo apt install --no-install-recommends software-properties-common dirmngr
# add the signing key (by Michael Rutter) for these repos
# To verify key, run gpg --show-keys /etc/apt/trusted.gpg.d/cran_ubuntu_key.asc 
# Fingerprint: E298A3A825C0D65DFD57CBB651716619E084DAB9
wget -qO- https://cloud.r-project.org/bin/linux/ubuntu/marutter_pubkey.asc | sudo tee -a /etc/apt/trusted.gpg.d/cran_ubuntu_key.asc
# add the R 4.0 repo from CRAN -- adjust 'focal' to 'groovy' or 'bionic' as needed
sudo add-apt-repository "deb https://cloud.r-project.org/bin/linux/ubuntu $(lsb_release -cs)-cran40/"
sudo apt install --no-install-recommends -y r-base r-base-dev
```

The above commands are for installing version 4+ R. For other versions, refer to
the documents at https://cran.r-project.org/bin/linux/ubuntu/.


## Install Rstudio server

To install the latest version of Rstudio server, go to the page
https://posit.co/download/rstudio-server/ and find the corresponding .deb
package for the OS. Here is the example to install Rstudio server in
Ubuntu 22.04.

```bash
sudo apt-get install gdebi-core
wget https://download2.rstudio.org/server/jammy/amd64/rstudio-server-2023.03.1-446-amd64.deb
sudo gdebi rstudio-server-2023.03.1-446-amd64.deb
```

If the command 'gdebi' does not work, one can use

```bash
sudo apt install rstudio-server-2023.03.1-446-amd64.deb
```

## Configure Rstudio server

### User management

To create/add a new user *rstudio*, use the following command

```bash
sudo adduser rstudio # this also create a new group rstudio if not exist
# add user rstudio to sudo group
sudo usermod -aG sudo rstudio # this will also prompt for password, which will be used for rstudio login
```

In default, all existing users with uid > 100 can login into the rstudio server,
too. However, for EC2 users, they don't have a password. To create one password,
one need to run the following:

```bash
sudo passwd ubuntu # create new password for login to Rstudio server
# you can also add ubuntu to the group rstudio, run
sudo adduser ubuntu rstudio
```

### Server setting

One can skip this step if he wants to test the server quickly, because these
settings are for fine-tuning non-default values.

There are two configuration files:

- /etc/rstudio/rserver.conf: for the settings of the server, such as users, ports,etc.
- /etc/rstudio/rsession.conf: for sessions, such as session timeouts, user library
  paths, CRAN mirror, etc.
  
For the server settings, open /etc/rstudio/rserver.conf, and the following
parameters can be considered:

  * www-port=80: set server port to another such as 80, instead of the default
    8787.
  
  * www-address=127.0.0.1: set server to accept only specified clients, such this
    local client only. The default is to accept all the remote connections.
  
  * rsession-which-r=/opt/R/4.3.0/bin/R: specify which R should be used.
  
  * rsession-ld-library-path=/opt/local/lib:/opt/local/someapp/lib : add elements
    to the default LD_LIBRARY_PATH, in case some dependent packages are installed
    in a non-standard locations
  
  * auth-required-user-group=rstudio_users,ubuntu: limit server access to specific groups
  
  **Important** for the settings to take effect, one need to restart the server

For the rsession setting, one can open /etc/rstudio/rsession.conf and consider
the following parameters:

  * session-timeout-minutes=90: change session timeout to 90 minutes if the user
    is inactive. Default is 120 minutes.
  
  * r-libs-user=/opt/R/packages: set the user library location for new packages
    to install.
  
  * r-cran-repos=https://cran.rstudio.com/: set the default CRAN mirror site
  
### Common Rstudio server commands

- Verify the server installation and setting

  ```
  sudo rstudio-server verify-installation
  ```

- Start and stop server

  ```
  sudo rstudio-server stop
  sudo rstudio-server start
  sudo rstudio-server restart
  ```
  
- List active R sessions

  ```
  sudo rstudio-server active-sessions
  ```

- Suspend/kill R sessions

  ```
  sudo rstudio-server suspend-session <pid>
  sudo rstudio-server suspend-all
  sudo rstudio-server kill-session <pid>
  sudo rstudio-server kill-all
  ```

- Take server online or offline for maintenance

  ```
  sudo rstudio-server offline
  sudo rstudio-server online
  ```


## Start the server and login

Start the server

```bash
sudo rstudio-server restart
```

To connect, input `http://your-ec2-ip-address:8787` in your browser (any client),
you should see the login page, please input the username and password to login.

Hooray!!! Now you can use Rstudio anywhere.

Happy programming, :smile:


## Troubleshooting

- Error: "R graphics engine version 16 is not supported by this version of RStudio. The Plots tab will be disabled until a newer version of RStudio is installed"

  * This error is due to the misalignment between the R version and rstudio-server
    versions. Basically, graphics engine version 14, 15, and 16 are provided by
    R version 4.1.x, 4.2.x, and 4.3.x, and the correct version of R studio should
    be the one published after the R version published. Also see this issue
    https://github.com/rstudio/rstudio/issues/12751.
  
- The Rstudio-server for Ubuntu 18.04

  * At the Rstudio website, as of 08/25/2023, it only provides compiled versions
    of Rstudio-server for Ubuntu 20+. To get the compiled versions for Ubuntu 18.04,
    one can go to https://dailies.rstudio.com/rstudio/elsbeth-geranium/server/bionic-amd64/.
    Actually, the same files can be found at AWS S3 folder
    rstudio-ide-build/server/bionic/amd64/.

- Error: "Unable to load shared library libssl.so.xx" when starting Rstudio-server.

  * This is normally due to a wrong version of Rstudio-server being installed, for
    example, a compiled version for Ubuntu 20 is installed in Ubuntu 18.04. Make sure
    the compiled version is for the operating system under consideration.


## References

- Install Rstudio server in AWS: https://jagg19.github.io/2019/08/aws-r/#long_way

- Rstudio user management: https://docs.posit.co/ide/server-pro/authenticating_users/restricting_access.html

- Rstudio server admin guide: https://s3.amazonaws.com/rstudio-server/rstudio-server-pro-0.98.507-admin-guide.pdf

- Configure Rstudio server: https://support.posit.co/hc/en-us/articles/200532327-Configuring-the-Server
