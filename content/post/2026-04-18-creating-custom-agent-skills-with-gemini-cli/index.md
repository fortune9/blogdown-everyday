---
title: '[AI] Creating Custom Agent Skills with Gemini CLI: A Step-by-Step Guide'
author: Zhenguo Zhang
date: '2026-04-18'
slug: creating-custom-agent-skills-with-gemini-cli
categories:
  - AI
  - Blogging
tags:
  - gemini-cli
  - agent-skills
  - tutorial
description: 'Learn how to extend Gemini CLI by creating custom agent skills with specialized workflows and templates.'
featured_image: ''
images: []
comment: true
---

Agent skills are a powerful way to extend the capabilities of Gemini CLI. They allow you to package specialized procedural knowledge, workflows, and templates into a modular unit that the agent can "learn" and apply.

In this post, I will walk you through the process of creating a custom agent skill, using a real-world example: building a **Blogdown Post Manager** to automate my blogging workflow.

## 1. Initializing the Skill

The easiest way to start is by using the built-in `skill-creator` agent. It provides a template structure and validation tools.

**Prompt to Agent:**
> "I want to create an agent skill to help me manage blog posts. It should handle folder structure, YAML headers, and support both .md and .Rmd formats."

The agent will typically use the `init_skill.cjs` script:

```bash
node <path-to-skill-creator>/scripts/init_skill.cjs blogdown-post-manager --path .
```

This creates a folder structure like this:
- `SKILL.md`: The "brain" of the skill containing instructions.
- `assets/`: For templates or boilerplate files.
- `scripts/`: For executable automation.
- `references/`: For extra documentation.

## 2. Defining the "Brain" (SKILL.md)

The `SKILL.md` file contains the frontmatter (trigger description) and the markdown body (the instructions).

Example `SKILL.md` frontmatter:
```yaml
---
name: blogdown-post-manager
description: Create or update blog posts in a blogdown project with correct folder structure and YAML headers. Use when the user says "create blog post" or "update blog post".
---
```

In the body, you define the workflow. For our blog manager, we specified:
- Folder naming: `content/post/yyyy-mm-dd-[slug]/index.md`
- YAML header standards.
- Interactive gathering of metadata (asking for categories/tags).

## 3. Adding Assets

If your skill requires templates, place them in the `assets/` folder. For our blog manager, we created `assets/post.md` and `assets/post.Rmd` templates so the agent doesn't have to "guess" the YAML structure every time.

## 4. Packaging the Skill

Once your instructions and assets are ready, you need to package the skill into a `.skill` file. This file is essentially a ZIP archive that the Gemini CLI can interpret.

The most reliable way to do this is using the `package_skill.cjs` script provided by the `skill-creator` skill. If you have installed your skills in the standard user directory, the path will look like this:

```bash
# Example if installed in your home folder (.agents)
node ~/.agents/skills/skill-creator/scripts/package_skill.cjs blogdown-post-manager .
```

If the `skill-creator` is part of your global `node_modules` (e.g., via NVM), the path might be:
`~/.nvm/versions/node/vX.X.X/lib/node_modules/@google/gemini-cli/bundle/builtin/skill-creator/scripts/package_skill.cjs`

**Pro Tip:** If you don't want to hunt for the script, you can simply zip the folder yourself:
```bash
zip -r blogdown-post-manager.skill blogdown-post-manager/
```
The packaging script is preferred because it **validates** your `SKILL.md` for errors before creating the archive.

## 5. Installation

There are several ways to install and share your skill.

### For Gemini CLI Only

You can install it to your project (workspace) or globally (user).

**Workspace Scope (Current Project):**
```bash
gemini skills install blogdown-post-manager.skill --scope workspace --consent
```

**Global Scope (All Projects):**
```bash
gemini skills install blogdown-post-manager.skill --scope user --consent
```

### For All Agents (Shared Workflow)

If you use multiple agents (like `qwen-code` or `codex`), you can store the skill source in a standard directory that any agent can read. 

A good practice is to use a `.agents/skills/` folder in your project root:

1. Move the skill folder: `mv blogdown-post-manager .agents/skills/`
2. Point your agents to the `SKILL.md` file in that folder.

## 6. Activation

After installing, don't forget to reload the skills in your active session:

```bash
/skills reload
```

Now, whenever you say "create a blog post," Gemini CLI will activate your custom instructions and follow your specific workflow!

## 7. Final product

You can see my final version of this skill at https://github.com/fortune9/blogdown-everyday/tree/master/.agents/skills/blogdown-post-manager

