---
title: '[Python]Generators'
author: Zhenguo Zhang
date: '2018-10-17'
slug: python-generators
categories:
  - Computing
tags:
  - Python
---

## Definition

Generators are special python objects that have the following features:

1. iteratable. so one can use *next()* or with for loop to get the elements.
2. return elements when asked, one element each time; unlike list, the elements are not generated until asked for.
3. the local state is saved after each call/request, so it can resume when next call arrives.

## Advantages

Because of the above features, generators are more memory efficient than
lists, and thus used in handling large datastream.

## How to construct

There are two ways to make generators, summarized in the following table:

Category | Explanation
------ | ------
Generator functions | use *yield* other than *return* to return a value from a function.
Generator expression  | replace the surrounding '[]' of list comprehension with '()'.

Below are examples for each of them:

```Python
# Example 1: generator functions
def countdown(num):
    print('Starting')
    while num > 0:
        yield num
        num -= 1
a=countdown(10) # returned is a generator
next(a) # get next value
for i in a:
  print(a) # print each element

# Example 2: generator expression
mylist = ['a', 'b', 'c', 'd']
g = (x+’ZZG’ for x in mylist) # a generator
for s in g:
  print(s) # print each element
```

In the above first example, *yield* is used to return a value, and in
the second example, a generator is constructed in the format of 
'(xx for xx in an-iterator)'.

## Notes

1. A generator object can only be looped over once. To loop again,
regenerate it.

2. Generators are more efficient than list only when the dataset size is near or over
the limit of momery.

Enjoy programming :smiley:

## References

1. https://realpython.com/introduction-to-python-generators/



