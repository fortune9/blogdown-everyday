---
title: '[blogdown] Enable Disqus comment in Hugo website'
author: Zhenguo Zhang
date: '2021-06-10'
slug: blogdown-enable-disqus-comment-in-hugo-website
categories:
  - Blogging
tags:
  - blogdown
  - Hugo
description: ''
featured_image: ''
images: []
comment: yes
---

To enable [disqus](https://disqus.com/) in a blog website,
one need do setting in the configuration file 'config.toml'.

Depending on the theme used by the website, one need set
up the parameter 'disqusShortname', in the top level of
the config file, or under '[params]' section. The best
place to check this is to read the theme's github page
for configuration. For the theme 'diary', it need be under
'[params]', as below:

```
[params]
disqusShortname = "fortune9-netlify"
```

To get 'disqusShortname', one need visit https://disqus.com/admin/create/ to create one.

Happy programming. :smiley: