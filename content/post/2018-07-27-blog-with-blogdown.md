---
title: Blog with "blogdown"
author: Zhenguo Zhang
date: '2018-07-27'
slug: blog-with-blogdown
categories:
  - Blogging
tags:
  - R
  - blogdown
  - Hugo
---

I am very excited that I can start writing blogs with Markdown
(even better, Rmarkdown), and then push the posts to
GitHub. Then done!! The posts will show up at https://zhenguozhang.zone/ hosted at https://netlify.com.
I don't need worry deployment of the website.

For this to work, I used the excellent R package
*blogdown*, written by the R celebrity [Yihui Xie](https://yihui.name/en/).
Since the process is so amazing, I would like to share
it with you, so you can also benefit from it.

# Steps

1. Install the package *blogdown* in Rstudio by typing:

    ```r
install.packages("blogdown")
```

2. Create a website project by clicking the menus/buttons
in Rstudio in the following order:

    **File** --> **New Project...** --> **New Directory**
--> **Website using blogdown**. Then you will see the
following screen:

    ![](/post/images/project-window.png)

    Input the project folder name (say "test-blog") 
under "Directory name" 
and select where to put it by clicking "Browse...".
And finally click **Create Project**. Keep the other
options as default.

    blogdown will handle the rest, including installing
necessary software.

3. Click the **New Post** under the menu **Addins**,
fill the relevant information, and then you can start
writing the post in the edit pane of Rstudio. After editting,
save the post.

4. Preview the website locally by click **Serve Site**
under the menu **Addins**.

5. Push it to Github if it looks good. 
First, create a repository at Github, and let's say the
repository name is "test-blog" and the github username
is 'fortune9'. Then, in the command line,
go to the site folder "test-blog" created in the Step 2,
and type:
```bash
git init
git add *
git commit -m "my blog started"
# important: change repository and username portions in following URLs
git add remote origin https://github.com/fortune9/test-blog.git
git push -u origin master
```

6. Deploy the website to netlify.com.
Go to the website https://www.netlify.com/ to sign up a
free account, and link your github repository to create
a website. See https://www.netlify.com/blog/2016/09/29/a-step-by-step-guide-deploying-on-netlify/ for step-by-step guide.
When configurating the settings, keep "Branch" as "master",
"Dir" as "public", and "Build command" as "hugo".

7. Enjoy your website and blogging.

# Afterword

1. You may fail in the deployment. If this happens,
that may be caused by the lower version of hugo used
by netlify. To solve this, add a file "netlify.toml"
in the root of site folder "test-blog", and put the
following code in it:
```
[context.production.environment]
  HUGO_VERSION = "0.45.1"
```
Then commit and push the changes to github, and check
the deployment in a couple of minutes.

2. You can see Yihui's talk on blogdown at 
https://www.rstudio.com/resources/videos/create-and-maintain-websites-with-r-markdown-and-blogdown/
In the talk, he also demonstrated how to type 
SUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUUPER fast using HHKB.

3. The blogdown is built on [hugo](https://gohugo.io/),
an automatic static site generator, so you may learn it
if you want to configure the website better.

4. Actually, I tried to build my blog firstly with hugo
directly by following the instruction at https://github.com/netlify/victor-hugo and
https://www.netlify.com/blog/2016/09/21/a-step-by-step-guide-victor-hugo-on-netlify/, 
but I failed during rendering the website, possibly
because the files are too old to be used by the lastest
software.

5. I started to use blogdown because of the inspiration
from [Peng Zhao](http://www.pzhao.org/)
and [Yixuan](https://statr.me/).

Please write me if you have any comments or questions.