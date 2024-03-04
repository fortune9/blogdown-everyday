---
title: '[Linux] How to clone a private git repo in Dockerfile'
author: Zhenguo Zhang
date: '2024-03-03'
slug: linux-how-to-clone-a-private-git-repo-in-dockerfile
categories:
  - Computing
  - Software
tags:
  - git
  - github
  - docker
  - Bash
description: ''
featured_image: ''
images: []
comment: yes
---

Sometimes, one may want to download code from a private repo
when building a docker image based on a *Dockerfile*.
However, the process building the docker image doesn't have
the same privilege as the host running `docker build`. Fortunately,
the newer version docker has provided the capability for us to
pass git secrets to the image-building process. Let's see how.


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

Let's say you have a `RUN` command needing to access a private repo in the
above mentioned github account, you can use the following command:

```bash
# add the repo host to know_hosts, only run once
RUN mkdir -p -m 0700 ~/.ssh && ssh-keyscan github.com >> ~/.ssh/known_hosts
# the command need accessing the private repo
RUN --mount=type=ssh,id=github \
    git clone git@github.com:fortune9/bioinfo-tools.git
    
```

Save the `Dockerfile`.

## Step 4: build the image


```bash
docker buildx build --ssh github=$SSH_AUTH_SOCK -f Dockerfile .
```

Note that I used **github** as the id for the secret in step 3 and 4, you can
change it into anything as long as you use the same value all the time.

I hope that this helps you to build a great docker image.

Happy programming :smile:

## Reference

- More about accessing secrets from Docker file: https://docs.docker.com/reference/dockerfile/#run---mounttypesecret
