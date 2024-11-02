---
title: '[R] How to install R package from a private git repo in Dockerfile'
author: Zhenguo Zhang
date: '2024-11-02'
slug: r-how-to-install-r-package-from-a-private-git-repo-in-dockerfile
categories:
  - R
  - Computing
  - Software
tags:
  - R
  - docker
description: ''
featured_image: ''
images: []
comment: yes
---

What if you see a great R package, but it is in a private git repo;
moreover, you need to build a docker image and install the package
into it? The main challenge is how can you provide the git credentials
to the package installer in Dockerfile.

I did Google search and found this [post](https://medium.com/@erika_dike/installing-package-from-a-private-repo-in-your-docker-container-f45b1a4954a2),
in which a Personal Access Token (PAT) is passed into docker building process
via an argument. This is OK but one still has to store the PAT somewhere
during the process.

In this post, I will introduce a different approach, using docker build's
secret-passing feature. Let's go.

I use an example demo repo for this purpose, when you implement it,
you can use your own repo.

The approach applies to both gitlab.com and github.com repos.

## Step 1: add an ssh key to github account

Follow [this post](https://fortune9.netlify.app/2023/07/01/tutorial-github-gitlab-ssh-key-setup/)
to generate and add ssh key to your github account, and this github account
should contain the private repo to access from Dockerfile.

Let's say the ssh key local file is at `~/.ssh/id_rsa_github`.

For more info, see this [link](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account).


## Step 2: add the ssh key to ssh agent

Use the following command

```bash
eval $(ssh-agent)
ssh-add ~/.ssh/id_rsa_github # replace this ssh key file with your own file
```

## Step 3: use --mount=type=ssh in Dockerfile to access the added ssh key

Let's say you have a `RUN` command needing to install R package from a private repo in the
above mentioned github account, you can use the following command:

```bash
# add the repo host to know_hosts, only run once
RUN mkdir -p -m 0700 ~/.ssh && ssh-keyscan github.com >> ~/.ssh/known_hosts
# the command need accessing the private repo
RUN --mount=type=ssh,id=github \
     R -e "install.packages('remotes'); remotes::install_git('git@github.com:fortune9/r-package.git@tag-1')" \
     && rm -rf /tmp/*
```

Note that we use the function `remotes::install_git()` to install the
R package, the url must be in the ssh-compatible format, and additionally
we specified a specific tag version `tag-1` to install. No need to provide
PAT to the function at all.


Save the `Dockerfile`. Note, also add `# syntax=docker/dockerfile:1` as
the first line in Dockerfile.

## Step 4: build the image


```bash
docker buildx build --ssh github=$SSH_AUTH_SOCK -f Dockerfile .
```

Note that I used **github** as the id for the secret in step 3 and 4, you can
change it into any git servers as long as you use the same value all the time.

I hope that this helps you to build a great docker image.

Happy programming :smile:
