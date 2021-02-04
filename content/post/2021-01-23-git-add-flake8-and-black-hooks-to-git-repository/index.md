---
title: '[git] add flake8 and black hooks to git repository'
author: Zhenguo Zhang
date: '2021-01-23'
slug: git-add-flake8-and-black-hooks-to-git-repository
categories:
  - Computing
tags:
  - Programming
  - Python
---

[PEP8](https://www.python.org/dev/peps/pep-0008/) provides 
guideline on writing python code. To make the compliance
to the guideline easy, one can automate this process. There
are two ways to achieve this: pre-commit check and github
actions. Today, we will talk about how to implement pre-commit
check.

pre-commit check installs hooks to a git repo, and when committing
new code, the hooks will be triggered. Let's start.

## Step 1. Install pre-commit package

```bash
pip install pre-commit
```

After this, add 'pre-commit' to the 'requirements.txt' file so
that this package is always installed.

## Step 2. Add hooks

One need create a config file *.pre-commit-config.yaml* at the
root of the repo to add hooks.

One example configuration with *black* and *flake8* hooks is
as follows:

```yaml
# See https://pre-commit.com for more information
# See https://pre-commit.com/hooks.html for more hooks
repos:
  - repo: https://github.com/psf/black
    rev: stable
    hooks:
      - id: black
        language_version: python3.7
  - repo: https://gitlab.com/pycqa/flake8
    rev: 3.8.4
    hooks:
      - id: flake8
```

The config file is basically telling pre-commit where to download
hooks and which hook version to use. See https://pre-commit.com/hooks.html for more information.

## Step 3. Install hooks

To install the configured hooks to the repo, one need run

```bash
pre-commit install
```

and this will install hooks to the *.git/hooks* folder.

## Step 4. Configure flake8

To make the hooks run as expected, one may want to set up
configuration for flake8, one example file is like this:

```flake8
[flake8]
ignore = E203, E266, E501, W503, F403, F401
max-line-length = 79
max-complexity = 18
select = B,C,E,F,W,T4,B9
```

This config file sets some options used by `flake8`.

## Step 5. Configure Black

Similarly, one can also configure black. For this, one
need do it in the *pyproject.toml* file. One example file
is as follows:

```toml
[tool.black]
py36 = true
line-length = 79
include = '\.pyi?$'
exclude = '''
/(
    \.git
  | \.hg
  | \.mypy_cache
  | \.tox
  | \.venv
  | _build
  | buck-out
  | build
  | dist

  # The following are specific to Black, you probably don't want those.
  | blib2to3
  | tests/data
)/
```

Here, we set parameter *line-length* to match *max-line-length*,
otherwise *black* and *flake8* will conflict. For other parameters,
check the command options of *black*.

## Step 6. Format existing files

This step is optional, but for existing files in the project,
one may run the following command to format existing files:

```bash
pre-commit run --all-files
```

All done!!!

Then from now on, every time a commit is added to the repo,
the hooks will be triggered. If any changes are made by
*black*, the commit would fail. One can review the changes,
and modify if necessary, and commit again, until succeed.

# References

1. https://dev.to/m1yag1/how-to-setup-your-project-with-pre-commit-black-and-flake8-183k

2. https://medium.com/staqu-dev-logs/keeping-python-code-clean-with-pre-commit-hooks-black-flake8-and-isort-cac8b01e0ea1
