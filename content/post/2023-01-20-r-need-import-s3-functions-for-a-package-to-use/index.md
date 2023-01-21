---
title: '[R] Need import S3 functions for a package to use'
author: Zhenguo Zhang
date: '2023-01-20'
slug: r-need-import-s3-functions-for-a-package-to-use
categories:
  - R
  - Computing
  - Software
tags:
  - Programming
  - R
description: ''
featured_image: ''
images: []
comment: yes
---

When developing an R package, it has been suggested to
just put the dependent packages in *Description* file
under *Imports* section.

However, I recently found that for S3 methods exported by
a package, one need to import such functions explicitly
in order to use them in the developed package. One example
is the *unique()* function from the package *data.table*.
This function can't be called via *data.table::unique()*,
because it is a S3 method. However, if it is not imported,
the method *uniq.data.frame()* would be used instead.

To import it, one can add the following roxygen2 comment
above the calling function:

```
#' @importFrom data.table unique
```

Then run *devtools::document()* to update relevant files,
including *NAMESPACE*.

This is just a quick note in case similar problems are met in future.

:smile:

Happy Chinese Lunar New Year!

