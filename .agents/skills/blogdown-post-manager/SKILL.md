---
name: blogdown-post-manager
description: Create or update blog posts in a blogdown project with correct folder structure and YAML headers. Use when the user says "create blog post" or "update blog post".
---

# Blogdown Post Manager

This skill helps manage blog posts in a `blogdown` website, ensuring consistent folder structure, YAML headers, and content formatting.

## Workflow

### 1. Identify the Task
Determine if you are creating a new post or updating an existing one.

### 2. Gather Metadata Interactively (New Posts)
When creating a new post, you MUST NOT guess the metadata. Instead, interact with the user to gather the following fields:
- **Title**: Ask for the post title (suggest prefixing with `[R]` or `[Python]` if applicable).
- **Categories & Tags**: Suggest categories and tags by looking at existing posts in `content/post/`.
- **Images**: Ask if any featured images or gallery images should be included.
- **Format**: Ask if the post should be `.md` or `.Rmd`.

### 3. Folder Structure & Path
All posts MUST be stored in their own subdirectories under `content/post/`.
- **Format**: `content/post/yyyy-mm-dd-[slug]/index.[md|Rmd]`
- **Example**: `content/post/2026-04-18-r-how-to-modify-the-theme-used-by-blogdown/index.md`

### 4. YAML Header Requirements
- **Update**: Keep the YAML header intact if it already exists.
- **Create**: Use the gathered metadata. Default author to `Zhenguo Zhang` and date to current date.

```yaml
---
title: '[R] My Post Title'
author: Zhenguo Zhang
date: '2026-04-18'
slug: my-post-slug
categories:
  - Blogging
tags:
  - R
description: ''
featured_image: ''
images: []
comment: true
---
```

### 5. Content & Code
- Ensure all code blocks are functional and correctly formatted.
- For `.Rmd` files, include the setup chunk:
  ````r
  ```{r setup, include=FALSE}
  knitr::opts_chunk$set(echo = TRUE)
  ```
  ````

## Usage Examples

### Creating a New Post
**User**: "Create a blog post about ggplot2 barplots."
**Agent**:
1. "I'll help you create that. First, what should be the exact title? (e.g., '[R] Creating Barplots with ggplot2')"
2. "For categories, you've used 'R' and 'Visualization' before. Should I use those?"
3. "Would you like to include any featured images or a description?"
4. "Should I use .Rmd format since this involves R code?"
5. (After user confirms) -> Create folder and `index.Rmd`.

### Updating an Existing Post
**User**: "Update the blog post about blogdown themes to add a section on submodules."
**Agent**:
1. Find existing file; default path looks like
   `content/post/yyyy-mm-dd-[slug]/index.md`.
2. Read the file, keeping the YAML header unchanged.
3. Append or insert the new content.
