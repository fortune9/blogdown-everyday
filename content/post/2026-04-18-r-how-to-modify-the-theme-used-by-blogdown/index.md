---
title: '[R] How to modify the theme used by blogdown?'
author: Zhenguo Zhang
date: '2026-04-18'
slug: r-how-to-modify-the-theme-used-by-blogdown
categories:
  - Blogging
  - R
tags:
  - blogdown
  - Hugo
description: ''
featured_image: ''
images: []
comment: true
---

My website is built using `blogdown` and published on Netlify via CI/CD. Recently, I updated my Hugo version from 0.92 to 0.154.2. Unfortunately, this update broke the deployment pipeline due to an incompatibility in one of the files within the `diary` theme.

When your Hugo theme is no longer compatible with a newer Hugo version or if you simply want to customize its behavior, there are two primary ways to handle it.

## Solution 1: Overriding Theme Files Locally

The simplest way to modify a theme without changing the theme's source code is to take advantage of Hugo's lookup order. Hugo prioritizes files in your root project folder over those in the `themes/` directory.

### Step-by-step instructions:

1. Identify the file in the theme that needs modification (e.g., `themes/diary/layouts/_default/single.html`).
2. Create a corresponding directory structure under your root `layouts/` folder if it doesn't exist.
3. Copy the file from the theme folder to your root folder:
   ```bash
   mkdir -p layouts/_default
   cp themes/diary/layouts/_default/single.html layouts/_default/single.html
   ```
4. Modify `layouts/_default/single.html` as needed. Hugo will now use your local version instead of the one in the theme.

## Solution 2: Forking the Theme Repository

If you have many changes or want to manage the theme's source code directly, forking the theme is a better long-term solution. Since `blogdown` (and Hugo) typically manages themes as Git submodules, you'll need to update the submodule to point to your fork.

### Step-by-step instructions:

1. **Fork the repository**: Go to the original theme repo [hugo-theme-diary](https://github.com/AmazingRise/hugo-theme-diary.git) and fork it to your own GitHub account.
2. **Clone your blog repo** (if not already local): `https://github.com/fortune9/blogdown-everyday.git`.
3. **Update the submodule URL**:
   Update your submodule to point to your forked URL:
   ```bash
   git submodule set-url themes/diary https://github.com/YOUR_USERNAME/hugo-theme-diary.git
   ```
4. **Sync and Update**:
   ```bash
   git submodule sync
   git submodule update --init --recursive
   ```
5. **Apply your changes**: Go into the `themes/diary` directory, make your fixes, commit, and push them to your fork.
6. **Commit the submodule change in your blog repo**:
   Back in the root of your blog repo, commit the change to the submodule pointer:
   ```bash
   git add themes/diary
   git commit -m "Switch diary theme to personal fork and apply Hugo compatibility fixes"
   git push
   ```

By following either of these methods, you can ensure your blog remains compatible with the latest Hugo versions while maintaining your custom styles and fixes.
