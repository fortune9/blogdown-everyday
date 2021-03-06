---
title: '[R] Add a footer to the outside of rmarkdown html main-container'
author: Zhenguo Zhang
date: '2021-06-06'
slug: r-add-a-footer-to-the-outside-of-rmarkdown-html-main-container
categories:
  - R
  - Tips
tags:
  - R
  - R Markdown
description: ''
featured_image: ''
images: []
comment: yes
---

Normally, in the html output of an Rmarkdown document,
the `<body>...</body>` tag encloses a `<div>...</div>`
with class 'main-container' which hold the main content.
When one add a footer with the following YAML header, it
will appear at the bottom of the page as expected:

```
output: 
  html_document:
    includes:
      after_body: resource/footer/banner.html
```

However, this may lead to problems when the main-container
is in a frame or a mini-page: the added footer will fill to
the width of the frame, but not to the width of the page.

To have a footer to fill the full width of a page, one has
two options: one is to modify the Rmarkdown template used
for building output, and the other is to use javascript to
arrange the page component. Here, I will demonstrate the second
option, and readers can click [here](https://github.com/rstudio/rmarkdown/issues/1761)
for more details on the first option.

```js
$(function() {
  $('.main-container').after($('.footer'));
})
```

The above javascript will put components with 
`class="footer"' after the class 'main-container'.

To have the footer in the right place, we also need
set up css rules:

```css
html {
  position: relative;
  min-height: 100%;
}

body {
  margin-bottom: 70px; # leave space for the footer
}
.footer {
  position: absolute; # keep it always at bottom
  bottom: 0;
  width: 100%;
  height: 70px;
  padding: 10px 5px;
  background: yellow;
}
```

Now let's add a footer using custom block:

```
::: {.footer}
A footer created with Javascript
[Contact](mailto:zhangz.sci@gmail.com)
:::
```

Alternatively, one can use `htmltools::includeHTML(file)`
to add raw html code if the footer is stored in a file.

:smile: :clap:

## References

1. Discussion of this topic at github: https://github.com/rstudio/rmarkdown/issues/1761

