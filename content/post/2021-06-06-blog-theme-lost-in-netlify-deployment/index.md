---
title: '[Blog] theme lost in Netlify deployment'
author: Zhenguo Zhang
date: '2021-06-06'
slug: blog-theme-lost-in-netlify-deployment
categories:
  - Blogging
  - Software
tags:
  - blogdown
  - Hugo
  - R Markdown
description: ''
featured_image: ''
images: []
comment: yes
---

Yesterday, I wrote a blog in Rmarkdown and pushed it to
github for automatic deployment in Netlify.com.

Although the website was displayed correctly, it was not
in the deployed website -- the text were shown in disorder
and the theme seemed not used.

I guess that the following issues may cause the problem:

1. in that Rmarkdown post, I used javascript code to change
the page layout (adding a footer).

2. I also used CSS to modify html layout.

These settings may conflict with the settings in the theme,
leading to the problem.

Even if I reverted the github changes to remove the post,
the issue remained, possibly due to the cache mechanism
in the Netlify.

Finally, I had to create a new netlify website name to
deploy from the same github repo, and it worked. Following
that, I changed the new site name to the old one, and stopped
deploying in the old website.

A lesson learned is: Don't activate CSS and javascript in
Rmarkdown blog posts.

:sleepy: :night_with_stars:
