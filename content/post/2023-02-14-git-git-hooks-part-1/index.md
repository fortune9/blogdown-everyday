---
title: '[Git] Git hooks - Part 1'
author: Zhenguo Zhang
date: '2023-02-14'
slug: git-git-hooks-part-1
categories:
  - Computing
  - Software
tags:
  - github
  - git
  - Computer
  - Programming
description: ''
featured_image: ''
images: []
comment: yes
---

## Introduction

[Git hooks](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks)
allows one to customize git operations automatically, such as
checking the code before commit, notify others when committed,
or test the code before pushing.

Basically, hooks can be any executable files put under the folder
*.git/hooks*, and named according to the trigger event, such as
*.git/hooks/pre-commit* (triggered when a commit is being generated).
The executables can be written in bash, perl, python, R, or anything
that can be executed.

## List of hooks

Here are a list of hooks, which can be divided into two categories:
client-side and server-side. The former are triggered by the events
locally, while the latter are run at the servers hosting the code
such as github.com.

Hook | Side
:--- | --- 
applypatch-msg  | client
pre-applypatch | client
post-applypatch | client
pre-commit | client
prepare-commit-m | clientg
commit-msg | client
post-commit | client
pre-rebase | client
post-checkout | client
post-merge | client
pre-receive | server
update | server
post-receive | server
post-update | client
pre-auto-gc | client
post-rewrite | client
pre-push | client


## Sharing hooks

Since hooks are stored in *.git/hooks* and this folder are
not pushed to a repository, one can't share the hooks directly.
To work arround it, one can put the hooks in a folder outside
*.git* and let others to copy them to *.git/hooks*.

Alternatively, one can use the [pre-commit framework](https://pre-commit.com/)
to share hooks via git repository.


## References

- Git hooks: https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks

- Get Started with Git Hooks: https://medium.com/@f3igao/get-started-with-git-hooks-5a489725c639

- Git hooks tutorial: https://www.atlassian.com/git/tutorials/git-hooks

- Public hooks: https://githooks.com/

- A framework for managing pre-commit hooks: https://pre-commit.com/