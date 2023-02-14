---
title: '[Github] Gist is essentially git repo'
author: Zhenguo Zhang
date: '2023-02-14'
slug: github-gist-is-essentially-git-repo
categories:
  - Computing
tags:
  - github
  - gists
description: ''
featured_image: ''
images: []
comment: yes
---

[Gists](https://gist.github.com/) are usually single pages,
for users to show information such as manual, tutotials, etc.

Actually, they are git repos, too, so one can clone and
push to it. To do so, one need to go to a gist page,
for example: https://gist.github.com/fortune9/85cb4f128ee5537c4a8cfdf6f2497e84.
And then choose "Clone via HTTPS" from the dropdown menu
at the top right of the page (as shown in the below figure), and copy the URL after the selection.

![](/post/images/gist-clone-screenshot.png)

Open a new command terminal and run the following command:

```bash
git clone https://gist.github.com/85cb4f128ee5537c4a8cfdf6f2497e84.git
# rename the cloned folder to whatever you like
mv 85cb4f128ee5537c4a8cfdf6f2497e84 bioresource
cd bioresource
# do some changes and commit them
git push # to add the changes to remote
```

Actually, because gist is a repo, so one can have multiple
files and push them, but github will show these files
by stacking them vertically.

Happy programming :wink: