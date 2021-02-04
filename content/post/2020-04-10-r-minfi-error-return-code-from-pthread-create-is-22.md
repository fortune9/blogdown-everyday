---
title: '[R] minfi ERROR; return code from pthread_create() is 22'
author: "Zhenguo Zhang"
date: '2020-04-10'
slug: r-minfi-error-return-code-from-pthread-create-is-22
tags:
- R
- Research
categories:
- R
- Computing
---

[minfi](https://www.bioconductor.org/packages/release/bioc/html/minfi.html)
is an R package to analyze DNA methylation arrays, such Illumina 450K
and EPIC arrays.

One main function in the minfi package is *estimateCellCounts*; it can
be used to estimate immuno cell fractions from a sample based on DNA
methylation values. However, recently when I tried to run the function,
I met the following problem during the normalization step:

```
Loading required package: FlowSorted.Blood.450k
Loading required package: IlluminaHumanMethylation450kmanifest
Loading required package: IlluminaHumanMethylationEPICmanifest
[preprocessFunnorm] Background and dye bias correction with noob
Loading required package: IlluminaHumanMethylation450kanno.ilmn12.hg19
[preprocessFunnorm] Mapping to genome
[preprocessFunnorm] Quantile extraction
[preprocessFunnorm] Normalization
Error in preprocessCore::normalize.quantiles.use.target(matrix(crtColumn.> reduced),  :
   ERROR; return code from pthread_create() is 22
```

I used the parameter 'processMethod="preprocessFunnorm"' in the above run,
but using other parameter values led to the same errors.

## Causes

After hours of research, I found that this error was caused by different
versions of **R**. Simply, I used the [Anaconda](https://www.anaconda.com/distribution/)
R when running the above command, which led to errors. When I swithed
to official R installed by following the [guide](https://cloud.r-project.org/bin/linux/ubuntu/README.html)
, the program ran smoothly.

A deeper reasearch revealed that the Anaconda R uses an in-built blas
from the library *libRblas*, other than standard/system blas library
(such as mkl blas or openblas), even these system libraries are installed
already. One can run *sessionInfo()* and find the used blas library at
the section "Matrix products: default". This libRblas blas seems
incompatible with the functions in the minfi package (particularly those
using multiple threads).

The R from official R website uses the system blas library (possibly
also lapack library).

## Solutions

Note, the solution is for **Ubuntu Bionic Beaver (18.04;LTS)**. You need
change some values accordingly if using a different system.

So here is the (solution)[https://cloud.r-project.org/bin/linux/ubuntu/README.html], 
install the official R. The steps are as follows:

1. put the following lines into */etc/apt/sources.list* or a text file in
   */etc/apt/source.list.d/*.

        deb https://cloud.r-project.org/bin/linux/ubuntu bionic-cran35/
        deb http://us-east-1.ec2.archive.ubuntu.com/ubuntu/ bionic-backports main restricted universe

    You need change these values to match your system.
   
2. run the following commands

        sudo apt-get update
        sudo apt-get install r-base r-base-dev

3. Finally install *minfi* by following the bioconductor page.
   During this step, you may find errors such as the package 'httr' isn't
   available. If this happens, try to install this manually.

## Methods not working

1. installing *bioconductor-minfi* package using conda.

## Bonus

This [page](https://csantill.github.io/RPerformanceWBLAS/) describes more
on different versions of BLAS, and how to setup default one with
*update-alternatives* command. Please have a look.
