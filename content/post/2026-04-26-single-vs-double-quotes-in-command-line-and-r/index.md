---
title: 'Single vs Double Quotes in Command Line and R'
author: Zhenguo Zhang
date: '2026-04-26'
slug: single-vs-double-quotes-in-command-line-and-r
categories:
  - Linux
  - R
tags:
  - bash
  - regex
  - quotes
  - escaping
description: ''
featured_image: ''
images: []
comment: true
---

When feeding a program with command line arguments, single quotes and double quotes make a significant difference.

### The Shell Rule

Single quotes (`'`) do not allow escaping any characters; the contents are treated literally. Double quotes (`"`), however, allow for character escaping and variable expansion.

You can test this using the `printf` command, which prints each argument it receives on a new line:

```bash
# Using single quotes: the backslash is literal
printf "%s\n" '\+hello'
# Output: \+hello

# Using double quotes: the shell escapes the double backslash
printf "%s\n" "\\+hello"
# Output: \+hello
```

As you can see, both commands result in the same string `\+hello` being passed to the program. The former is read exactly as written, while in the latter, the shell interprets the double backslash as a single literal backslash before passing it along.

### The R Console Rule

In R, when you work directly in the R console, you **must deal with the language's own string literal syntax**.

Most programming languages, including R, use the backslash (`\`) as an escape character. So when you type it,
it will be explained as an escape character immediately. If you type `"\."` in R console, it will be interpreted
in the console first before being passed to R itself; the interpreter will likely tell you there is no such escape sequence as `\.`, and it won't actually put a backslash in the resulting string.

To place a literal backslash in a string in R, you have to use a double backslash (`\\`). This tells R that you don't want the second backslash to be an escape character when typing **in the console**, but rather that you want a single literal backslash character in the string to be passed to R.

For instance, when defining a regular expression:

```r
myregexp = "^planet\\.name$"
```

This creates a string containing the text `^planet\.name$` (note the single backslash). This string is then passed to a regex function, which interprets the `\.` sequence as "a literal dot" rather than the regex wildcard for "any character."

This rule also applies to the scenario where you type the escaping characters in R scripts.

For more depth on this, check out this [Reddit discussion on double slashes in regex](https://www.reddit.com/r/learnprogramming/comments/13bb5pa/why_double_slashes_in_regex_expressions/).

### Command Line Arguments for R Scripts

Crucially, if you are running an R script and feeding it parameters from the command line (e.g., via `Rscript`), the
above **the shell rule** apply instead of the internal R console rules for those initial arguments.

To see this in action, create a file named `test_args.R`:

```r
# test_args.R
args <- commandArgs(trailingOnly = TRUE)
if (length(args) > 0) {
    cat("Argument received by R:", args[1], "\n")
}
```

Now run it from your terminal:

```bash
# Test with single quotes
Rscript test_args.R '\+hello'
# Output: Argument received by R: \+hello

# Test with double quotes
Rscript test_args.R "\\+hello"
# Output: Argument received by R: \+hello
```

If you want to input a regular expression to an R script via the command line for use in a function like `sub()`, you should use these shell quoting rules. The string `\+hello` received by R will correctly match the literal string `+hello` in your R code.
