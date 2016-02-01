---
layout: post
title: "Python Basics 2: Iterators, For Loops, and Comprehensions"
author:
    name: Sean Raven
    github: dragon0
description: ""
category: lessons
tags: ["python"]
---
{% include JB/setup %}

This article covers iterators, `for` loops, and comprehensions.

## Iterators

Collections have a mechanism for accessing each item in sequence.
This mechanism is called an `iterator`.
Collections which support iteration are said to be `iterable`.
Iterable collections have an `__iter__` method which returns an `iterator` object.
Iterator objects are defined by the methods `__iter__` and `__next__`.
An iterator's `__iter__` method must return itself,
and its `__next__` method must either return the next item in the sequence
or raise a `StopIteration` exception.

The iterator for a collection should be obtained using the built-in function `iter`.
If the argument to `iter` defines `__iter__`, then it is called;
otherwise, `iter` will return a default iterator.
The default iterator assumes the collection's `__getitem__` method accepts
contiguous, nonnegative `int` keys starting at `0` and raises `IndexError` when
the key is out of bounds.

Some iterable collections also support reverse iteration, defined by the
`__reversed__` method and `reversed` built-in function.

There is also a built-in `next` function, which calls `__next__` on its argument.

{% highlight pycon %}
>>> lst = [1, 2, 3]
>>> it = iter(lst)
>>> next(it)
1
>>> next(it)
2
>>> next(it)
3
>>> next(it)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
>>> rit = reversed(lst)
>>> next(rit)
3
>>> next(rit)
2
>>> next(rit)
1
>>> next(rit)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
{% endhighlight %}

Documentation for
[iter](https://docs.python.org/3/library/functions.html#iter),
[reversed](https://docs.python.org/3/library/functions.html#reversed),
and
[next](https://docs.python.org/3/library/functions.html#next).

### Built-In Functions That Accept Iterables

- [sorted](https://docs.python.org/3/library/functions.html#sorted):
  Return a new sorted list from the items in an iterable.
  For sorting examples and a brief sorting tutorial, see
  [Sorting HOW TO](https://docs.python.org/3/howto/sorting.html#sortinghowto).
- [sum](https://docs.python.org/3/library/functions.html#sum):
  Sums the items in a iterable and returns the result.
- [max](https://docs.python.org/3/library/functions.html#max):
  Return the largest item in an iterable or the largest of two or more
  arguments.
- [min](https://docs.python.org/3/library/functions.html#min):
  Return the smallest item in an iterable or the smallest of two or more
  arguments.
- [all](https://docs.python.org/3/library/functions.html#all):
  Return `True` if all elements of the iterable are true (or if the iterable is
  empty).
- [any](https://docs.python.org/3/library/functions.html#any):
  Return `True` if any element of the iterable is true. If the iterable is
  empty, return `False`.

## For Loops

Python's `for` loop only works for iterables.

{% highlight python %}
for item in sequence:
    print(item)
{% endhighlight %}

`sequence` is evaluated, and the interpreter retrieves an `iterator` from the
result.
The iterator's `__next__` method is called, and the return value is stored in
`item`.
The loop terminates when the `__next__` method raises `StopIteration`.

Python will apply sequence unpacking on `item`, allowing you to extract the
items in nested sequences as long as you know the size of the inner sequences:

{% highlight pycon %}
>>> points = [(0, 0), (3, 5), (4, 7), (9, 11)]
>>> for point in points:
...     x, y = point
...     print('({}, {})'.format(x, y))
... 
(0, 0)
(3, 5)
(4, 7)
(9, 11)
>>> for x, y in points:
...     print('({}, {})'.format(x, y))
... 
(0, 0)
(3, 5)
(4, 7)
(9, 11)
{% endhighlight %}

If you want to loop over a sequence of integers (like C++ or Java `for` loops),
use the [range](https://docs.python.org/3/library/stdtypes.html#typesseq-range)
type:

{% highlight pycon %}
>>> for i in range(5):
...     print(i)
... 
0
1
2
3
4
>>> for i in range(3, 6):
...     print(i)
... 
3
4
5
>>> for i in range(2, 8, 2):
...     print(i)
... 
2
4
6
{% endhighlight %}

Like `while` loops, `for` loops can have an `else` clause.
The `else` clause is only executed if the loop terminates by reaching the end of
the sequence (ie it does not execute if the loop terminates due to a `break`
statement).

{% highlight pycon %}
>>> for n in range(2, 15):
...     for x in range(2, n):
...         if n % x == 0:
...             print(n, '=', x, '*', n//x)
...             break
...     else:
...         # loop fell through without finding a factor
...         print(n, 'is prime')
... 
2 is prime
3 is prime
4 = 2 * 2
5 is prime
6 = 2 * 3
7 is prime
8 = 2 * 4
9 = 3 * 3
10 = 2 * 5
11 is prime
12 = 2 * 6
13 is prime
14 = 2 * 7
{% endhighlight %}

To iterate over the *indices* of a sequence, use `range` with `len`:

{% highlight python %}
for index in range(len(sequence)):
    print(sequence[index])
{% endhighlight %}

You can get the index and corresponding item using the
[enumerate](https://docs.python.org/3/library/functions.html#enumerate)
function:

{% highlight python %}
for index, item in enumerate(sequence):
    print("sequence[{}] = {}".format(index, item))
{% endhighlight %}

You can iterate over parallel sequences using
[zip](https://docs.python.org/3/library/functions.html#zip):

{% highlight python %}
for item1, item2 in zip(sequence1, sequence2):
    print(item1, item2)
{% endhighlight %}

From [the docs on zip](https://docs.python.org/3/library/functions.html#zip):

> The left-to-right evaluation order of the iterables is guaranteed. This makes 
possible an idiom for clustering a data series into n-length groups using 
`zip(*[iter(s)]*n)`. This repeats the same iterator n times so that each output 
tuple has the result of n calls to the iterator. This has the effect of 
dividing the input into n-length chunks.

{% highlight pycon %}
>>> for x, y, z in zip(*[iter(range(10))]*3):
...     print(x, y, z)
... 
0 1 2
3 4 5
6 7 8
{% endhighlight %}

Note that `zip` truncates on the shortest sequence, which is why `9` didn't
appear in the output above.
To work around this, use
[itertools.zip_longest](https://docs.python.org/3/library/itertools.html#itertools.zip_longest).

{% highlight pycon %}
>>> import itertools
>>> for x, y, z in itertools.zip_longest(*[iter(range(10))]*3):
...     print(x, y, z)
... 
0 1 2
3 4 5
6 7 8
9 None None
>>> for w, x, y, z in itertools.zip_longest(*[iter(range(10))]*4, fillvalue=0):
...     print(w, x, y, z)
... 
0 1 2 3
4 5 6 7
8 9 0 0
{% endhighlight %}

There are other useful functions in the
[itertools](https://docs.python.org/3/library/itertools.html) module.

## List, Set, and Dictionary Comprehensions

List Comprehensions offer concise syntax to create lists.
Comprehensions are often faster than the equivalent loops, because they can be
optimized by the interpreter.

{% highlight python %}
squares = []
for x in range(10):
    squares.append(x**2)

# or
squares = [x**2 for x in range(10)]
{% endhighlight %}

List Comprehensions can contain multiple `for` and `if` clauses.

{% highlight python %}
combs = []
for x in [1,2,3]:
    for y in [3,1,4]:
        if x != y:
            combs.append((x, y))
# or
combs = [(x, y) for x in [1,2,3] for y in [3,1,4] if x != y]
{% endhighlight %}

Note how the order of the `for` and `if` statements is the same in both cases.

Python also supports `set` comprehensions.

{% highlight pycon %}
>>> {x for x in 'abracadabra' if x not in 'abc'}
{'r', 'd'}
{% endhighlight %}

Python also supports `dict` comprehensions.

{% highlight pycon %}
>>> {x: x**2 for x in (2, 4, 6)}
{2: 4, 4: 16, 6: 36}
{% endhighlight %}

