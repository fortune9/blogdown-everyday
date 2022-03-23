---
title: '[R] Include external script or child documents in Rmarkdown'
author: Zhenguo Zhang
date: '2022-03-22'
slug: r-include-external-script-or-child-documents-in-rmarkdown
categories:
  - R
  - Software
tags:
  - R
  - R Markdown
  - Child document
  - external script
description: ''
featured_image: ''
images: []
comment: yes
---

When one Rmarkdown document is too big, one can consider
split the file into smaller ones. In this post,
I will introduce the ways for this purpose.

## Include external R scripts

There are two ways to include external scripts:
using the functions `source()` or `sys.source()`
or chunk option `file`.

### Method 1: use `source()` or `sys.source()`

````md
```{r, include=F}
source("external.R", local=knitr::knit_global())
# or sys.source("external.R", envir=knitr::knit_global())
```
````

Note that one needs to provide local environment
explicitly with `knitr::knit_global()`; otherwise
it may report errors of objects not found.

This method has a disadvantage: the input source
code can't be displayed or not formatted properly
if this is a feature needed, and this issue goes
away if one use the chunk option `file`.

### Method 2: use chunk option `file`

````md
```{r, file="external.R"}
```
````

With this method, one can also include code from
other languages such as python, C++, etc.

## Include external Rmarkdown

To include a child rmarkdown document, there are
alsoe two ways: using the chunk option `child` and
use the function `knitr::knit_child()`.

### Use chunk option `child`

````md
```{r, child="child.Rmd"}
```
````

With this method, one can conditionally include a
document given that all chunk options can take values
from arbitrary R expressions. For example:

````md
```{r, child=if(x>10) 'child1.Rmd' else 'child2.Rmd'}
```
````

### Use the function `knitr::knit_child()`

`knitr::knit_child()` can convert an Rmarkdown
document to a character vector of markdown format, which can be printed
out into current document directly, like below:

````md
```{r, echo=FALSE, results='asis'}
knitStr <- knitr::knit_child('child.Rmd', quiet = TRUE)
cat(knitStr, sep = '\n')
```
````

I hope that this summary gives you some ideas of how
to include external documents in an Rmarkdown document.
From here, you can dig deeper by reading other documents
such as the link listed at [Reference](#reference).


## Reference

1. Rmarkdown-cookbook: https://bookdown.org/yihui/rmarkdown-cookbook/managing-projects.html

