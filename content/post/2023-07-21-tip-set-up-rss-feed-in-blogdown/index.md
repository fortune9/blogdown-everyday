---
title: '[Tip] Set up RSS feed in blogdown and add it to R-bloggers'
author: Zhenguo Zhang
date: '2023-07-21'
slug: tip-set-up-rss-feed-in-blogdown
categories:
  - R
  - Blogging
  - Tips
tags:
  - R
  - blogdown
  - Hugo
description: ''
featured_image: ''
images: []
comment: yes
---

Hugo website comes with a default RSS template, which controls
the format of the RSS feed for all pages in a website. One
can check the RSS output by typing the following URL in the web browser:

> https://my-website.com/index.xml

The default output seems containing a full content for the '<description>'
field, but when checking a section/sub-website, the content in '<description>' seems
trucated. For example, checking the following URL:

> https://my-website.com/categories/r/index.xml

However, showing the full content in the '<description>' field is required by
adding one's blog to the website https://www.r-bloggers.com/.

Then how can we create an RSS feed for website sections in a blogdown website?

Actually, I searched google, found multiple articles as listed in
[References](#references), but they are possibly outdated and not
working any more. Below is the procedure I used and it worked:

## Create a new rss.xml under the folder layouts/categories/

To do so, I just simply copied the RSS template file from
the theme folder (the theme used is https://github.com/amazingrise/hugo-theme-diary)
using the following command:

```bash
mkdir -p layouts/categories
./themes/diary/layouts/index.rss.xml layouts/categories/rss.xml
```

Note, run the commands from the root folder of your blogdown website.

I also don't have any file in the folder `layouts/_default`. Some articles
said there is a default RSS file in this folder, but in my case, I did not
see any, so I used the file `themes/diary/layouts/index.rss.xml`.

## Then make the following changes

The changes made are listed for each file, just skip it if your files
already satisfy the conditions.

- layouts/categories/rss.xml: in the '<description>' tag field,
  find '{{- .Summary | html -}}', and replace it with
  '{{- .Content | html -}}'

- config.toml: add the following section if not there yet,
  
  ```
  [Author]
    name = "Your name"
    email = "your-email@example.com"
  ```
  
  This change may not be essential, and the values here are
  just used by the feed output.

## Test it locally

A tip is to check the website before deploying it on a server (my case,
server is netlify). In Rstudio, typing

```r
blogdown:::serve_site()
```

And then you can test your website as well as feeds. If it fails,
this gives the earliest time to fix it.

One can check the feeds by typing the following in the browser:

- the whole site:
  > localhost:1234/index.xml

- categories:
  > localhost:1234/categories/r/index.xml

Here I checked the category 'r', and you can check any categories
defined in your website.

My local server has port 1234, please change as necessary to match
yours.

## Add link to r-bloggers.com

To add one's blog to r-bloggers.com, it was required that your website
provides a link to r-bloggers.com. Read the requirements [here](https://www.r-bloggers.com/add-your-blog/).

So I added a link at the sidebar to https://www.r-bloggers.com/, and hope
that this satisfies the requirement.

**Don't forget to push all changes to deploy them**.

## Submit a form

Don't forget to submit a form at https://www.r-bloggers.com/add-your-blog/.

Since my blog contains content beyond R, I provided my R categories
at the field 'Blog RSS feed', more specifically: https://fortune9.netlify.app/categories/r/index.xml

I am waiting for an update for my blog to show up at r-bloggers.com, which
may take some time.


## References

Here are some articles I found; as I said, most of them are already
outdated.

- https://coolbutuseless.bitbucket.io/2018/02/07/blogdown-rss-feed-of-full-articles/

- https://yongfu.name/2018/12/13/hugo_rss/

- https://gohugo.io/templates/rss/


Happy programming :smile:!
