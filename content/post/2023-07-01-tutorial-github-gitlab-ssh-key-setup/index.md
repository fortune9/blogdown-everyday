---
title: '[Tutorial] Github/gitlab ssh key setup'
author: Zhenguo Zhang
date: '2023-07-01'
slug: tutorial-github-gitlab-ssh-key-setup
categories:
  - Computing
  - Software
tags:
  - github
  - git
  - SSH
description: ''
featured_image: ''
images: []
comment: yes
---


One can connect to gitlab or github via ssh and https. Today, I will explain
how to generate ssh key, add it to the github/gitlab account, and connect to
github/gitlab using ssh in Ubuntu.

Since github and gitlab are similar, I will use github as example here:

## Generate ssh key

```bash
ssh-keygen -f ~/.ssh/id_rsa_github
```

The above command will generate two files: id_rsa_github and id_rsa_github.pub.
The latter is the public key and should be copied to the github account. Please
answer any prompts during the process such as passphrase accordingly.

To get the public key string, run the following command:

```bash
cat ~/.ssh/id_rsa_github.pub
```

Copy the key string and add it to github account in the next step.

## Add the key to your github account

Click your profile photo at github.com and then 'Settings', followed by
`SSH and GPG keys`, then 'Add SSH Key', and paste the public key to the
*Key* field.


## Configure SSH to point to the right key

Then open the config file `~/.ssh/config` and add the following into it:

```bash
Host github.com
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/id_rsa_github
```

## Use the SSH key to connect

Now one can use the following command to connect to github,
for example:

```bash
git clone git@github.com:fortune9/github-test.git
```

Congratulations! You make it!

## References

- Add github SSH key: https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account

- Gitlab ssh setting: https://docs.gitlab.com/ee/user/ssh.html
