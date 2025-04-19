---
title: '[git] Exclude a path when staging files'
author: Zhenguo Zhang
date: '2025-04-19'
slug: git-exclude-a-path-when-staging-files
categories:
  - Computing
  - Software
tags:
  - git
description: ''
featured_image: ''
images: []
comment: yes
---

Occasionally, you may want to exclude a specific path when staging files in Git. This can be useful when you have a directory with many files, but you only want to stage some of them. Today I will show how to do it.

## Preparation

First, let's clone a git repository and create some files to demonstrate the process.

The repository used here is one of my own, which contains some bioinformatics tools. You can use any repository you like, or create a new one.

```bash
git clone https://github.com/fortune9/bioinfo-tools.git
cd bioinfo-tools
```

Let's create some new files

```bash
touch new.txt
mkdir excluded
touch excluded/tmp1.txt
touch excluded/tmp2.txt
```

## Exclude a folder

First, let's check the status of the repository to see what files are untracked.

```bash
git status
```

And you will see something like this, depending on your repository:

```
On branch master
Your branch is up to date with 'origin/master'.

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        excluded/
        new.txt

nothing added to commit but untracked files present (use "git add" to track)
```

With the following command, you can add all files except the `excluded` directory.

```bash
git add . ':!excluded'
```

Note that the excluded path must following the following format:

- in single quotes, double quotes would trigger shell errors
- starts with ':!'

With the following command to check what have been staged:

```bash
git status
```

And you can see something like:

```
On branch master
Your branch is up to date with 'origin/master'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   new.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        excluded/
```

## Exclude more than one path at a time

Actually, one can exclude multiple paths at a time, and the below commands
show this.

First, let's roll back the staged files.

```bash
git restore --staged .
```

And then, let's exclude two files in the `excluded` folder (essentially the
same effect as excluding the whole folder, but just for demonstration purpose):

```bash
git add . ':!excluded/tmp1.txt' ':!excluded/tmp2.txt'
```

And you can check the status again.

I hope that this tip is helpful for you. Happy coding! :smile:

