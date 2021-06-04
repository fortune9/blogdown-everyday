---
title: '[R] Install multiple versions of R'
author: Zhenguo Zhang
date: '2021-06-02'
slug: r-install-multiple-versions-of-r
categories:
  - Computing
  - R
tags:
  - Linux
  - R
description: 'how to install multiple versions of R in a system'
featured_image: ''
images: []
comment: yes
---

Often, one may need a specific version of R and associated packages
to R a program. Recently, I tried to use the R package
[DSS](https://www.bioconductor.org/packages/release/bioc/html/DSS.html),
which depends on another package [BiocParallel](https://www.bioconductor.org/packages/release/bioc/html/BiocParallel.html). 
I found that the latest version of DSS package in BioConductor
version 3.13 didn't work well (very slow when using multiple cores). Therefore, I wanted to downgrade BioConductor and associated
packages to version 3.11, which I tested. To do so, I had to
install an old version R 4.0.0, which matched BioConductor 3.11.

To keep both latest version 4.1.0 and an old version 4.0.0 of R,
I need install/maintain multiple versions of R, and this is the
focus of this post.

After doing some research, I found that the resource given by the
Rstudio at https://docs.rstudio.com/resources/install-r/ is very
useful for the purpose, and here are the steps and code for me
to install and load different versions of R, more specifically,
version 4.1.0 (latest) and 4.0.0. All the supported versions of R
can be found at https://cdn.rstudio.com/r/versions.json.


## Installation

My system is Ubuntu 18.04, so I will show the code to install R under
Ubuntu, for other systems, please refer to https://docs.rstudio.com/resources/install-r/.

The bash code below will install two versions of R in the system.

```bash
rVersions=(4.1.0 4.0.0)
rBins=() # to hold the bin folders of all R versions
for rV in ${rVersions[@]}
do
  echo Installing R version $rV
  export R_VERSION=$rV
  curl -O https://cdn.rstudio.com/r/ubuntu-1804/pkgs/r-${R_VERSION}_1_amd64.deb
  printf "y" | sudo gdebi r-${R_VERSION}_1_amd64.deb
  if [[ $(/opt/R/${R_VERSION}/bin/R --version) ]]; then
    echo "Success on installing R $rV"
    rBins+=(/opt/R/${R_VERSION}/bin)
  else
    echo "Failed on installing R $rV"
    exit 1
  fi
done
```

## Setup the folders for R package installation

Here I set up a separate folder for each version of R
to install R packages, avoid conflicts among different
versions of R packages.

```bash
rPkgRoot=$HOME/RLib
for rV in ${rVersions[@]}
do
  rLib=$rPkgRoot/$rV
  mkdir -p $rLib
done
```

## Create a unique interface to call different R versions

One can save the following bash script into a file named
'r', change its mode with 'chmod +x r', 
and put it into a folder included in environment variable 
'$PATH'. After that one can call corresponding version of R
using `r 4.0.0` at the command line.

```bash
#!/bin/bash

if [[ $# -lt 1 ]]; then
  echo "$0 [<r-version> [other options passed to R] ]"
  echo "The default for r-version is 4.1.0"
  echo "Example: $0 4.0.0"
fi

rPkgRoot=$HOME/RLib # the root directory containing all libraries
rV=${1:-4.1.0}
shift
rLib=$rPkgRoot/$rV # default is R/R.version$platform-library/x.y for R x.y.z
rBin=/opt/R/$rV/bin
if [[ ! -x $rBin/R ]]; then
  echo "No executable '$rBin/R' exist"
  exit 1;
fi
export PATH=$rBin:$PATH
# create the folder, otherwise ignored by R
if [[ ! -d $rLib ]]; then
  mkdir -p $rLib
fi
# here I use 'NO_Main_Lib' to suppress my .Rprofile to add another user library
R_LIBS_USER=$rLib NO_Main_Lib=T R $@
```

Congratulations. You have multiple versions of R now.

Happy programming!

## References

1. BioManager::install: https://www.bioconductor.org/install/

2. R installation at R studio: https://docs.rstudio.com/resources/install-r/

3. Github page for the R builds of Rstudio: https://github.com/rstudio/r-builds

