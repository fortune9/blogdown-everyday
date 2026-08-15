---
title: '[Linux] Handling grep Exit Codes in Scripts with set -e and set -o pipefail'
author: Zhenguo Zhang
date: '2026-08-15'
slug: linux-handling-grep-exit-codes-in-scripts-with-set-e-and-set-o-pipefail
categories:
  - Linux
  - Shell
tags:
  - grep
  - bash
  - shell
  - error-handling
description: 'Learn why grep exit code 1 causes strict Bash scripts with set -e and set -o pipefail to terminate unexpectedly, and explore practical solutions to handle it safely.'
featured_image: ''
images: []
comment: true
---

When writing robust Bash scripts, it is common best practice to enable strict mode options:

```bash
set -e          # Exit immediately if a command exits with a non-zero status
set -o pipefail # Pipeline exit status is the value of the last (rightmost) command to exit with non-zero status
```

While these settings prevent errors from silently propagating, they frequently cause unexpected script failures when working with `grep`. 

In this post, we will explore why `grep` exit codes break strict shell scripts, demonstrate the problem with clear examples, and detail four practical solutions to address it.

---

## Understanding `grep` Exit Codes

Unlike many Unix tools where any non-zero exit code indicates a fatal error, `grep` uses exit codes to communicate search outcomes:

* **Exit Code `0`**: One or more matching lines were found.
* **Exit Code `1`**: **No** matching lines were found (a valid, expected outcome in many workflows).
* **Exit Code `2` (or >1)**: An error occurred (e.g., syntax error in regex, missing file, permission denied).

Because shell safety directives like `set -e` treats *any* non-zero status as a command failure, `grep` returning `1` when no matches are found will cause the shell script to abort immediately. Similarly, `set -o pipefail` causes pipelines containing `grep` to fail if no lines match the query.

---

## The Problem in Action

### Example 1: Direct `grep` under `set -e`

Consider a script designed to process error logs:

```bash
#!/usr/bin/env bash
set -e

echo "Starting log analysis..."

# Try to find warnings in the log
grep "WARNING" server.log > warnings.txt

echo "Analysis complete!"
```

If `server.log` contains no lines matching `"WARNING"`, `grep` finishes cleanly without printing anything and returns an exit code of `1`. 

Under `set -e`, the shell interprets this `1` as a fatal execution failure. The script exits instantly at the `grep` line—`warnings.txt` may be left empty, and `"Analysis complete!"` is never printed!

### Example 2: Pipelines under `set -o pipefail`

Now consider a pipeline processing input:

```bash
#!/usr/bin/env bash
set -euo pipefail

# Count how many critical errors occurred
CRITICAL_COUNT=$(cat app.log | grep "CRITICAL" | wc -l)

echo "Critical errors count: ${CRITICAL_COUNT}"
```

Without `set -o pipefail`, the pipeline exit status would be that of `wc -l` (`0`), hiding the fact that `grep` returned `1`. 

However, with `set -o pipefail`, `grep`'s exit code `1` propagates as the failure status of the entire pipeline. As a result, `set -e` terminates the script before `CRITICAL_COUNT` is assigned.

---

## Solutions

Here are four effective ways to safely handle `grep` when `set -e` or `set -o pipefail` is active.

### Solution 1: Append `|| true` (or `|| :`)

The simplest and most common quick fix is using the OR operator (`||`) followed by `true` (or the shell built-in `:`):

```bash
grep "WARNING" server.log > warnings.txt || true
```

#### How it works
If `grep` succeeds (exit code `0`), the right side of `||` is ignored. If `grep` exits with code `1` (or `2`), the `|| true` executes, returning exit code `0` and allowing `set -e` to continue.

#### Pros & Cons
* **Pros**: Simple, concise, one-liner fix.
* **Cons**: It masks **all** non-zero exit codes, including code `2` (e.g., missing file or invalid regex pattern).

---

### Solution 2: Test `$?` or Use Conditional Statements

If you want to distinguish between "no matches found" (code `1`) and a real error (code `2`), you can check the exit status explicitly.

#### Approach A: Direct `if` statement
In Bash, commands tested inside `if` statements or `if grep ...` conditions do **not** trigger `set -e`:

```bash
if grep "WARNING" server.log > warnings.txt; then
    echo "Warnings were found."
else
    echo "No warnings found (or grep error)."
fi
```

#### Approach B: Preserving and evaluating `$?`
To specifically handle exit code `1` vs `2`:

```bash
# Capture exit status without triggering set -e
grep "WARNING" server.log > warnings.txt || status=$?

case ${status:-0} in
    0)
        echo "Found matches."
        ;;
    1)
        echo "No matches found. Continuing execution..."
        ;;
    *)
        echo "An error occurred with grep (exit code ${status}). Exiting..." >&2
        exit "${status}"
        ;;
esac
```

#### Pros & Cons
* **Pros**: Precise error handling; preserves actual error detection for code `2`.
* **Cons**: Slightly more verbose.

---

### Solution 3: Use `awk` for Filtering

If you are filtering text in a pipeline under `set -o pipefail`, `awk` can serve as a robust drop-in replacement for `grep`. 

Unlike `grep`, `awk` returns exit code `0` when matching zero lines (unless an unhandled script/syntax error occurs inside `awk` itself).

#### Replacing `grep` with `awk`:

```bash
# Instead of: grep "PATTERN" input.txt
awk '/PATTERN/' input.txt > output.txt

# Instead of: cat log.txt | grep "CRITICAL" | wc -l
CRITICAL_COUNT=$(awk '/CRITICAL/' log.txt | wc -l)
```

You can even perform the counting directly in `awk`:

```bash
CRITICAL_COUNT=$(awk '/CRITICAL/ {count++} END {print count+0}' log.txt)
```

#### Pros & Cons
* **Pros**: Eliminates exit code `1` issues entirely in pipelines while still returning non-zero on syntax or file access errors.
* **Cons**: Syntax is slightly longer than standard `grep`.

---

### Solution 4: Temporary Toggling of `set +e` or `set +o pipefail`

If you have a block of commands where `grep` exit codes should not terminate the script, you can temporarily disable strict checking and re-enable it afterwards.

```bash
# Disable strict flags temporarily
set +e
set +o pipefail

grep "WARNING" server.log > warnings.txt
GREP_EXIT=$?

# Re-enable strict flags
set -e
set -o pipefail

if [ "${GREP_EXIT}" -eq 0 ]; then
    echo "Warnings found."
elif [ "${GREP_EXIT}" -eq 1 ]; then
    echo "No warnings found."
else
    echo "Grep failed with error code ${GREP_EXIT}." >&2
    exit "${GREP_EXIT}"
fi
```

#### Pros & Cons
* **Pros**: Gives explicit control over scope and allows inspecting exit codes without modifying the pipeline syntax.
* **Cons**: Requires remembering to restore `set -e` / `set -o pipefail`.

---

## Summary Comparison

| Method | Syntax | Handles Code 1 (No Match) | Catches Code 2 (Errors)? | Best Used For |
| :--- | :--- | :---: | :---: | :--- |
| **`\|\| true`** | `grep ... \|\| true` | Yes | ❌ Masked as 0 | Quick scripts, optional filters |
| **Conditional (`if` / `$?`)** | `grep ... \|\| status=$?` | Yes | ✅ Yes | Safe scripts needing exact error checks |
| **`awk` Filtering** | `awk '/pattern/' file` | Yes | ✅ Yes | Pipelines with `set -o pipefail` |
| **Flag Toggling** | `set +e ... set -e` | Yes | ✅ Yes | Complex blocks or multi-command sections |

---

## Conclusion

Understanding how `grep` exit codes interact with shell strict mode (`set -e` and `set -o pipefail`) will save you hours of debugging mysterious script exits (**The main reason I write this post**). Depending on your security and logging needs, using `|| true` works well for quick tasks, while `if`/`$?` status checking or `awk` pipeline filtering offers the most robust solution for production scripts.
