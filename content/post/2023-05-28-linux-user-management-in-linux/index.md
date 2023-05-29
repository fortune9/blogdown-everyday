---
title: '[Linux] User management in Linux'
author: Zhenguo Zhang
date: '2023-05-28'
slug: linux-user-management-in-linux
categories:
  - Tips
  - Software
tags:
  - Linux
  - Programming
description: ''
featured_image: ''
images: []
comment: yes
---

Here is a summary of commands for user management in Linux.

- see all existing groups
  
  ```bash
  getent group
  ```

- get all groups a user belongs to
  ```bash
  groups username
  ```

- create a new group
  ```bash
  sudo groupadd new_group
  ```
  
- add user to a group
  ```bash
  sudo adduser username groupname
  # or
  sudo usermod -aG groupname username
  ```
  
- create a new user
  ```bash
  sudo adduser username # this also create a new group `username`
  # to create a new user only in an existing group, do
  sudo useradd -G groupname newusername
  ```

To be updated in the future.

Happy programming. :smile:


  
  
