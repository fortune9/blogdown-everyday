---
title: '[Linux] How to tell a bash script is sourced or executed?'
author: Zhenguo Zhang
date: '2021-06-03'
slug: linux-how-to-tell-a-bash-script-is-sourced-or-executed
categories:
  - Computing
  - Tips
tags:
  - Linux
description: ''
featured_image: ''
images: []
comment: yes
---

In bash, a script can be both executed and sourced.

An example script as follows:

```bash
#!/bin/bash

if [[ "${BASH_SOURCE[0]}" == "$0" ]]; then
    echo "I am being executed"
    sourced=0
else
    echo "I am being sourced"
    sourced=1
fi

# this variable setting has no effect if executed, because
# the execution is in a sub-shell
export VAR_SOURCE="Only effective if sourced"

if [[ $sourced -gt 0 ]]; then
  echo "Done in source"
  return 0
else
  # call exit when sourcing a file will exit the terminal
  echo "Done in execute"
  exit 0
fi

```

## Distinguish between the two

To tell whether a script is being sourced or not, one
can use the test `[[ "${BASH_SOURCE[0]}" == "$0" ]]`,
where '${BASH_SOURCE[0]}' is the current script filename,
and '$0' is the same if being executed but empty string ""
if sourced, as shown in the above code.

## Some differences between sourcing and executing

1. export: the command `export` has no effect for the calling
    environment because it is executed in a sub-shell. To set
    variables in current environment, source the script.

2. exit and return: calling 'exit' when sourcing a script will
    close the terminal, use 'return' instead.
    
## References

1. Discussion of '$BASH_SOURCE' on stackoverflow: https://stackoverflow.com/questions/41837948/can-a-bash-script-determine-if-its-been-sourced

    