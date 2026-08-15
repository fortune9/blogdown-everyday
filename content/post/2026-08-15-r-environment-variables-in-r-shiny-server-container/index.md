---
title: '[R] Environment Variables in R Shiny-Server Container: Problem and Solutions'
author: Zhenguo Zhang
date: '2026-08-15'
slug: r-environment-variables-in-r-shiny-server-container
categories:
  - R
  - Docker
tags:
  - Shiny
  - Docker
  - Linux
  - Environment Variables
description: 'Understand why ENV variables set in Dockerfile disappear when Shiny Server launches R apps, and learn the two best ways to solve it.'
featured_image: ''
images: []
comment: true
---

When dockerizing an R Shiny application hosted via **Shiny Server** (built from shiny server docker image https://hub.docker.com/r/rocker/shiny), a common issue developers face is that environment variables set via `ENV` instructions in the `Dockerfile` (or passed at runtime via `docker run -e`) are completely missing inside the R Shiny app.

For example, if you set the following environment variable in your `Dockerfile`:

```dockerfile
ENV SHOULD_IN_SHINY="From dockerfile"
```

When your app launches and runs `Sys.getenv("SHOULD_IN_SHINY")`, it returns an empty string `""` instead of `"From dockerfile"`.
This is not a problem with Docker itself, but rather a consequence of how **Shiny Server** spawns R worker processes.

In this post, we will look into the root cause behind this behavior in Shiny Server and demonstrate the two recommended solutions to correctly expose environment variables to your R Shiny workers.

---

## The Root Cause: How Shiny Server Spawns R Processes

The reason environment variables do not carry over to your R session lies in how Shiny Server executes R worker processes inside the container. 

Shiny Server runs as a system service (typically as `root` or `shiny`). When launching an app instance, it re-executes R as the `shiny` unprivileged user using `su`. Specifically, the execution call combines two mutually exclusive flags:

```bash
su shiny --login --preserve-environment -c "... R ..."
```

Let's break down what these flags request from `su`:

1. **`--login` (`-l`)**: Starts a **login shell**. This intentionally **resets** the environment to a minimal whitelist (`HOME`, `PATH`, `USER`, `TERM`, etc.) and sources system startup profiles (`/etc/profile`, `~/.profile`).
2. **`--preserve-environment` (`-p`)**: Explicitly asks `su` to **keep** the current environment inherited from the caller (which includes Docker's `ENV` variables).

Because Linux's `su` utility cannot honor both conflicting behaviors, it chooses `--login` and prints a warning:

```text
su: ignoring --preserve-environment, it's mutually exclusive with --login
```

> **Note:** This warning is emitted by Shiny Server's underlying process execution call, not by the R app itself. Attempting to suppress or patch it requires modifying and rebuilding Shiny Server C++ code, which is rarely practical.

Because the **login shell wins**, all custom environment variables passed to the container via `ENV` or `docker run` are **wiped before R ever starts**.

---

## Solutions

Since patching Shiny Server is unnecessary, we can utilize the natural extension points provided by the login shell or R itself.

### Solution 1: Use `~/.profile` (Recommended for User-Level Shell Vars)

Since `--login` causes the shell to source `~/.profile` for the `shiny` user, we can write our environment variables to `/home/shiny/.profile` during the Docker build phase. 

In your `Dockerfile`:

```dockerfile
# Append environment variable to the shiny user's profile
RUN echo 'export SHOULD_IN_SHINY="From dockerfile"' >> /home/shiny/.profile
```

When Shiny Server executes `su shiny --login ...`, the login shell will read `/home/shiny/.profile` and load `SHOULD_IN_SHINY` into the environment right before starting the R process.

---

### Solution 2: Use `~/.Renviron` (Recommended for R-Specific Configs)

Alternatively, R automatically inspects and loads `~/.Renviron` on startup, right after the shell environment is initialized.

`~/.Renviron` is purpose-built for R:
- Scoped strictly to R processes.
- Does not use shell `export` keywords or `$` variable expansions.
- Uses simple `KEY=value` key-value pairs.

In your `Dockerfile`, set up `/home/shiny/.Renviron`:

```dockerfile
# Set up .Renviron for the shiny user
RUN echo 'SHOULD_IN_SHINY="From dockerfile"' >> /home/shiny/.Renviron \
    && chown shiny:shiny /home/shiny/.Renviron
```

In your Shiny app (`app.R` or `server.R`), you can access it reliably:

```r
should_in_shiny <- Sys.getenv("SHOULD_IN_SHINY")
# Returns: "From dockerfile"
```

---

## Summary

* **The Problem**: Shiny Server calls `su shiny --login --preserve-environment`, causing `su` to discard inherited environment variables (such as Docker `ENV`) in favor of a clean login shell.
* **Solution 1 (`~/.profile`)**: Append `export KEY="value"` to `/home/shiny/.profile` in your `Dockerfile`.
* **Solution 2 (`~/.Renviron`)**: Append `KEY="value"` to `/home/shiny/.Renviron` in your `Dockerfile`.

Both approaches integrate seamlessly with Docker builds and ensure your Shiny application receives all required configuration variables cleanly.
