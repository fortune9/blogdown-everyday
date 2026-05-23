---
title: 'Understanding the Z-Algorithm for Efficient String Matching'
author: Zhenguo Zhang
date: '2026-05-22'
slug: z-algorithm-for-string-matching
categories:
  - Algorithm
  - Programming
tags:
  - Z-algorithm
  - String Matching
  - python
description: 'A deep dive into the Z-algorithm for linear-time string matching, focusing on the mirroring strategy.'
featured_image: ''
images: []
comment: true
---

String matching is a fundamental problem in computer science. Given a text `T` and a pattern `P`, how can we efficiently find all occurrences of `P` in `T`? While naive approaches take `O(n * m)` time, the **Z-algorithm** allows us to solve this in **O(n + m)** time.

### The Problem: Matching Two Strings

Imagine we have:
- **Pattern (P):** `aabcaab`
- **Text (T):** `baabaabcaab`

We want to find where `aabcaab` occurs in the text. The Z-algorithm approaches this by constructing a new string `S = P + $ + T` (where `$` is a separator not present in `P` or `T`) and computing the **Z-array** for `S`.

### What is the Z-Array?

For a string `S` of length `n`, the Z-array is an array of length `n` where `Z[i]` is the length of the longest substring starting at `S[i]` which is also a prefix of `S`.

Example for `S = ` `aabcaab`:
```text
Index: 0 1 2 3 4 5 6
String: a a b c a a b
Z-val: 0 1 0 0 3 1 0
```
- `Z[0]` is usually defined as 0 or `n`.
- `Z[4] = 3` because "aab" at index 4 matches the prefix "aab".

### Why the Z-Algorithm is Fast: O(m + n)

The naive computation of the Z-array still takes `O(n^2)`. The Z-algorithm achieves `O(n)` by maintaining a **Z-box [L, R]**, which is the rightmost interval `[L, R]` such that `S[L..R]` is a prefix of `S`.

By reusing previously computed `Z` values, we avoid redundant character comparisons. How?

### The Mirroring Strategy: The Heart of the Algorithm

The most critical part of the algorithm is when we compute `Z[i]` and `i` falls within the current Z-box `[L, R]` (`L <= i <= R`).

#### The Setup
```text
Prefix: [0 . . . . . R-L]
          |         |
          V         V
String: [L . . i . . R] . . .
```

Since `S[L..R]` matches `S[0..R-L]`, any substring starting at `i` inside the Z-box has a "mirror" image starting at `k = i - L` in the prefix.

#### Case 1: Z[k] is contained within the Z-box (Z[k] < R - i + 1)
If the match at the mirror position `k` doesn't reach the boundary `R`, we know `Z[i]` must be exactly `Z[k]`. No new comparisons are needed!

```text
       k       R-L
       |       |
Prefix: [a b c] . . .
         \
          \ (mirror)
           \
String: . . [a b c] . . .
             |     |
             i     R
```

#### Case 2: Z[k] reaches or exceeds the boundary (Z[k] >= R - i + 1)
If the match at `k` goes beyond `R`, we only know that `S[i..R]` matches the prefix. We must manually check characters starting from `R+1` to see if the match extends further.

```text
       k       R-L
       |       |
Prefix: [a b c d e f]
         \
          \ (mirror)
           \
String: . . [a b c d ? ?]
             |     |
             i     R
```

Once we find the new boundary, we update `[L, R]` to `[i, new R]`.

### Python Implementation

Here is a concise Python implementation of the Z-algorithm:

```python
def compute_z(s):
    n = len(s)
    z = [0] * n
    l, r = 0, 0
    for i in range(1, n):
        if i <= r:
            z[i] = min(r - i + 1, z[i - l]) # note the index is i - l (letter l), not i - 1
        while i + z[i] < n and s[z[i]] == s[i + z[i]]:
            z[i] += 1
        if i + z[i] - 1 > r:
            l, r = i, i + z[i] - 1
    return z
```

### How to Find Matches: P in T

To find all occurrences of a pattern `P` in a text `T`, we use the following steps:

1.  **Construct a combined string:** Create a new string by joining the pattern and the text with a unique separator that appears in neither: `Combined = P + "#" + T` (here we use `#` as the separator).
2.  **Compute the Z-array:** Run the Z-algorithm on this combined string.
3.  **Identify matches:** Iterate through the Z-array values corresponding to the text portion (indices after the `#` separator).
4.  **Check for pattern length:** If any `Z[i]` is equal to the length of `P`, it means a match of the pattern `P` starts at that position in the text.

The separator is crucial because it ensures that the "match" does not extend beyond the length of the pattern itself, preventing false positives that might span across the boundary between `P` and `T`.

### Conclusion

The Z-algorithm's mirroring strategy is a brilliant example of using space (the Z-array) to save time. By keeping track of the furthest reach (`R`), we ensure that each character is effectively compared only a constant number of times.

For a more detailed breakdown and implementation details, check out the following links:

- CP-Algorithms: https://cp-algorithms.com/string/z-function.html
- GeeksforGeeks: https://www.geeksforgeeks.org/z-algorithm-linear-time-pattern-searching-algorithm/

