---
layout: post
title: "Python Basics 3: Functions and Generators"
author:
    name: Sean Raven
    github: dragon0
description: ""
category: lessons
tags: ["python"]
---
{% include JB/setup %}


## Functions

A *function* is a block of code whose location in memory is bound to a name.
The code can be executed by *calling* the function.
To call a function, write its name followed by a set of parentheses.
Functions (or, more accurately, *callables*) are objects which define a
`__call__` method.
When called, the `__call__` method is executed.

Functions are created using the `def` statement.

{% highlight pycon %}
>>> def foo():
...     print('hi')
... 
>>> foo
<function foo at 0x7f976a1f06a8>
>>> dir(foo)
[... '__call__', ...]
>>> foo()
hi
{% endhighlight %}

A function can return a value to its caller using the `return` keyword.

{% highlight pycon %}
>>> def foo():
...     print('hi')
...     return 'hello'
... 
>>> foo()
hi
'hello'
>>> val = foo()
hi
>>> val
'hello'
{% endhighlight %}

A function can return multiple values by using tuple packing.

{% highlight python %}
def make_point():
    return 3, 5

point = make_point()
x, y = point
# or
x, y = make_point()
{% endhighlight %}

If a function does not explicitly return a value, it automatically returns `None`.

### Scope

Each function has its own *scope*.
Names bound in the function's body are in the function's scope.
Names bound outside of any function are in the *module* scope.
When a name is referenced in a function, the interpreter searches for the name
in the function's scope first, then the scopes of any enclosing functions,
then the module scope.

In this example, the interpreter searches for `x` in `foo`'s scope, then the
module scope.
Initially, `x` is not bound to a value, so `NameError` is raised.
After binding `x` to a value (in the module scope), `foo` works.

{% highlight pycon %}
>>> def foo():
...     print(x)
... 
>>> foo()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<stdin>", line 2, in foo
NameError: name 'x' is not defined
>>> x = 3
>>> foo()
3
{% endhighlight %}

Here, `foo` binds `x` in its local scope, so `x` refers to its local value.
The module's `x` is unaffected.

{% highlight pycon %}
>>> def foo():
...     x = 5
...     print(x)
... 
>>> foo()
5
>>> x
3
{% endhighlight %}

When a name is bound in a function, that name always references the local value.

{% highlight pycon %}
>>> def foo():
...     print(x)
...     x = 5
... 
>>> foo()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<stdin>", line 2, in foo
UnboundLocalError: local variable 'x' referenced before assignment
{% endhighlight %}

A function can refer to and rebind a global variable by using the
[global](https://docs.python.org/3/reference/simple_stmts.html#the-global-statement)
keyword.

{% highlight pycon %}
>>> def foo():
...     global x
...     print(x)
...     x = 5
... 
>>> x
3
>>> foo()
3
>>> x
5
{% endhighlight %}

Note that using `global` is often an indicator of poor design.
Functions should only modify local variables whenever possible.

There is also the
[nonlocal](https://docs.python.org/3/reference/simple_stmts.html#nonlocal)
keyword.
This is used by nested function definitions to refer to a name in an enclosing
function's scope, but not a name in the module scope.

### Parameters and Arguments

A function can specify certain local names which are to be bound by the caller
of the function.
These are called *parameters* and are listed between the parentheses in the
`def` statement.
The caller of the function can specify the values of the parameters, called
*arguments*, between the parentheses in the call.

In this example, `foo`'s local name `x` is bound to the module name `x` in the
first call, the value `3` in the second call, and the value `'hello'` in the
third call.

{% highlight pycon %}
>>> def foo(x):
...     print(x)
... 
>>> foo(x)
5
>>> foo(3)
3
>>> foo('hello')
hello
{% endhighlight %}

Using a name as an argument to a function will cause the function's local name
to refer to the same memory location as the argument.
This is called *pass by pointer* or *pass by object reference* in other
languages.
This means that, although re-binding a parameter is not visible outside of the
function, modifying mutable values such as `list` or `dict` objects *is* visible
to the caller.

{% highlight pycon %}
>>> def foo(lst):
...     lst.append(5) # modifies the same memory location as mylst
...     lst = []      # binds lst to a different memory location than mylst
...     lst.append(6) # modifies the new list object; mylst is unaffected
... 
>>> mylst = []
>>> foo(mylst)
>>> mylst
[5]
{% endhighlight %}

Function parameters can have default values.

{% highlight pycon %}
>>> def foo(x=5):
...     print(x)
... 
>>> foo(3)
3
>>> foo()
5
{% endhighlight %}

Note that default values are evaluated only once, and the same object is
shared between calls.
This means that if the default value is mutable (such as a `list` or `dict`),
any modifications to it in one call will be present in subsequent calls.

{% highlight pycon %}
>>> def append5(lst=[]):
...     lst.append(5)
...     return lst
... 
>>> append5()
[5]
>>> append5()
[5, 5]
>>> append5()
[5, 5, 5]
>>> append5()
[5, 5, 5, 5]
{% endhighlight %}

If you want the default to be a `list` or `dict`, you probably want this:

{% highlight python %}
def f(a, L=None):
    if L is None:
        L = []
    #...
{% endhighlight %}

Since `bool(None)` evaluates to `False`, you might see code like this:
`L = L or []`.
However, this can lead to surprising results.
Empty sequences are also `False`, so if the caller supplies a list which happens
to be empty, this code will create and modify a new list, where the caller
expected the function to modify the supplied list.
To support modifying a supplied, empty list, you should explicitly compare the
parameter to `None`: `if L is None: L = []`

Arguments to functions can be passed by keyword, by specifying the parameter
name and its value.

{% highlight python %}
def hypotenuse(x, y):
    return (x**2 + y**2) ** 0.5

hypotenuse(y=4, x=3)
{% endhighlight %}

(note that there is a `hypot` function in the `math` module)


If a parameter name is prefixed with `*`, then that parameter will be a
`tuple` containing any extra positional arguments.

{% highlight pycon %}
>>> def foo(x, y, *args):
...     print("x", x)
...     print("y", y)
...     print("args", args)
>>> foo('bar', 'baz', 'blip', 'blop', 'henry')
x bar
y baz
args ('blip', 'blop', 'henry')
{% endhighlight %}

Normally, these "variadic" arguments will be last in the list of parameters,
because they scoop up all remaining input arguments that are passed to the
function. Any parameters which occur after the `*args` parameter are
*keyword-only* arguments, meaning that they can only be used as keywords
rather than positional arguments.

{% highlight pycon %}
>>> def concat(*args, sep="/"):
...    return sep.join(args)
... 
>>> concat("earth", "mars", "venus")
'earth/mars/venus'
>>> concat("earth", "mars", "venus", sep=".")
'earth.mars.venus'
{% endhighlight %}

A single `*` indicates that all subsequent parameters are keyword-only,
without collecting the extra positional arguments in a `tuple`.

{% highlight pycon %}
>>> def foo(a, b, *, c='c', d='d', e='e'):
...     print('a', a)
...     print('b', b)
...     print('c', c)
...     print('d', d)
...     print('e', e)
... 
>>> foo(1, 2, 3)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: foo() takes 2 positional arguments but 3 were given
>>> foo(1, 2, c=3)
a 1
b 2
c 3
d d
e e
>>> foo(1, 2, e=3)
a 1
b 2
c c
d d
e 3
>>> foo(1, 2)
a 1
b 2
c c
d d
e e
>>> foo(1)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: foo() missing 1 required positional argument: 'b'
{% endhighlight %}

If a parameter name is prefixed with `**`, then that parameter will be a
`dict` containing any extra keyword arguments.

{% highlight pycon %}
>>> def foo(a, b, **kwargs):
...     print('a', a)
...     print('b', b)
...     print('kwargs', kwargs)
... 
>>> foo(a='bar', b='baz', c='blip', d='blop', e='henry')
a bar
b baz
kwargs {'e': 'henry', 'd': 'blop', 'c': 'blip'}
{% endhighlight %}

If present, a `**` parameter must follow any `*` parameter.

Function arguments can be stored in a `tuple` or `list` and unpacked using `*`.

{% highlight python %}
point = (x, y)
hypotenuse(*point) # equivalent to hypotenuse(point[0], point[1])
{% endhighlight %}

Function arguments can be stored in a `dict` and unpacked using `**`.

{% highlight python %}
point = {'x': x, 'y': y}
hypotenuse(**point) # equivalent to hypotenuse(x=point['x'], y=point['y'])
{% endhighlight %}


### Lambdas

The keyword `lambda` is used to create small functions.
The body of a lambda can only contain one expression, whose value is returned.

{% highlight python %}
def square(x):
    return x ** 2

# or
square = lambda x: x ** 2

{% endhighlight %}

If a lambda returns a `tuple`, it must be parenthesized.
Here, the first line tries to create a `tuple` containing a `lambda` and the
expression `x ** 2`.
This is demonstrated better by lines 2 and 3: `foo` is a `tuple` containing
the `lambda` and `5`.
Enclosing the body of the lambda in parentheses works as intended,
creating a `lambda` which returns a `tuple`.

{% highlight pycon %}
>>> foo = lambda x: x, x ** 2
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'x' is not defined
>>> foo = lambda x: x, 5
>>> foo
(<function <lambda> at 0x7f976a1f0730>, 5)
>>> foo = lambda x: (x, x ** 2)
>>> foo(3)
(3, 9)
{% endhighlight %}

Note that while `def` is a *statement*, `lambda` is an *expression*.
This means that a `lambda` can appear in a larger expression, such as a function
call or `tuple`, `list`, or `dict` literal.

### Higher-Order Functions

A "higher-order" function is one which treats other functions as data.
This means that a higher-order function may accept other functions as parameters
or return new functions.
Since Python's functions are just objects bound to names, a function can be
passed to another function just by passing its name.
It is also common to pass a lambda to a higher-order function.

A simple higher-order function is `apply`, which takes a function and an
argument and returns the result of passing the argument to the function.

{% highlight python %}
def apply(f, arg):
    return f(arg)
{% endhighlight %}

Three common higher-order functions are `map`, `filter`, and `reduce`.
These are used to process sequences, and can often replace `for` loops.
Python's implementations can accept any iterable.

The [map](https://docs.python.org/3/library/functions.html#map)
function takes a function and an iterable as arguments and returns an
iterator whose `__next__` method returns the result of applying the function
to the next item in the sequence.

{% highlight pycon %}
>>> m = map(lambda x: x**2, range(3))
>>> next(m)
0
>>> next(m)
1
>>> next(m)
4
>>> next(m)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
{% endhighlight %}

Python's `map` can take multiple iterables, as long as the number of iterables
matches the number of parameters of the given function.

{% highlight pycon %}
>>> m = map(lambda x, y: x+y, [1, 2, 3], [4, 5, 6])
>>> list(m)
[5, 7, 9]
{% endhighlight %}

The [filter](https://docs.python.org/3/library/functions.html#filter)
function takes a *predicate* function and an iterable.
A predicate function takes one parameter and returns a `bool`.
`filter` returns an iterator over the items for which the predicate returns
`True`.

{% highlight pycon %}
>>> is_even = lambda x: x%2 == 0
>>> f = filter(is_even, range(10))
>>> next(f)
0
>>> next(f)
2
>>> next(f)
4
>>> next(f)
6
>>> next(f)
8
>>> next(f)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
{% endhighlight %}

The [reduce](https://docs.python.org/3/library/functools.html#functools.reduce)
function reduces an iterable to a single value.
It takes three values: a function, an iterable, and an initialized accumulator.
The function must take two parameters, the accumulator and the current item,
and returns an updated accumulator.
`reduce` returns the final accumulator.
In Python 2, `reduce` was a built-in function; in Python 3, `reduce` has been
moved to the `functools` module.

{% highlight pycon %}
>>> def calc_avg(acc, val):
...     total, count, avg = acc
...     total += val
...     count += 1
...     avg = total / count
...     return total, count, avg
... 
>>> from functools import reduce
>>> reduce(calc_avg, range(10), (0, 0, 0))
(45, 10, 4.5)
{% endhighlight %}

If the initial accumulator is omitted, the first item in the sequence is used.

{% highlight pycon %}
>>> from operator import mul
>>> reduce(mul, range(1, 10))
362880
{% endhighlight %}

There are other higher-order functions in the
[itertools](https://docs.python.org/3/library/itertools.html)
and
[functools](https://docs.python.org/3/library/functools.html)
modules.
The [operator](https://docs.python.org/3/library/operator.html)
module contains functions that represent Python's operators, for use with
higher-order functions.

## Generators

A `generator` is an object which looks like a function but behaves like an iterator.
A generator is defined like a function, except that the `yield` keyword is used
instead of the `return` keyword.
When called, the generator returns an iterator whose `__next__` method executes
code in the generator until it encounters a `yield` expression.
The argument to the `yield` expression is returned from the `__next__` method.
When the generator returns, the `__next__` method raises `StopIteration`.

A simple generator which mimics Python 2's `xrange` might look like this:

{% highlight python %}
def xrange(start, stop=None, step=1):
    if stop is None:
        stop = start
        start = 0
    val = start
    while val < stop:
        yield val   # return the value to the caller of __next__
        val += step
# end of generator, __next__ raises StopIteration
{% endhighlight %}

Generators can be used to create iterators with infinite size while using
constant memory.
The following `fib` generator generates the Fibonacci sequence without bound
while only using enough memory to store `a` and `b`.

{% highlight python %}
def fib():
    a, b = 0, 1
    while True:
        yield b
        a, b = b, a+b
{% endhighlight %}

The `yield` expression can also delegate to a sub-generator using `yield from`.

{% highlight pycon %}
>>> def flatten(lst):
...     for item in lst:
...         if isinstance(item, list):
...             yield from flatten(item)
...         else:
...             yield item
... 
>>> lst = [[1, 2], 3, [[4, 5], 6]]
>>> list(flatten(lst))
[1, 2, 3, 4, 5, 6]
{% endhighlight %}

Generators can also be used for lightweight coroutines, see
[PEP 342](https://www.python.org/dev/peps/pep-0342).

See the documentation for
[yield](https://docs.python.org/3/reference/expressions.html#yieldexpr).

### Generator Expressions

Like lambdas, generator expressions are a syntax for creating simple generators.
A generator expression is a comprehension enclosed in parentheses instead of
brackets.

{% highlight pycon %}
>>> g = (x**2 for x in range(3))
>>> g
<generator object <genexpr> at 0x7f976a27c1b0>
>>> next(g)
0
>>> next(g)
1
>>> next(g)
4
>>> next(g)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
>>> g = (x for x in range(10) if x % 2 == 0)
>>> next(g)
0
>>> next(g)
2
>>> next(g)
4
>>> next(g)
6
>>> next(g)
8
>>> next(g)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
{% endhighlight %}

Note that generator expressions can be used in exactly the same way as the
built-in functions `map` and `filter`.
It is mostly a matter of personal preference as to which is more readable.
In terms of speed, generator expressions are slower than `map` but faster than
`filter`.
Some simple results using [timeit](https://docs.python.org/3/library/timeit.html):

{% highlight pycon %}
>>> import timeit
>>> timeit.timeit('list(str(n) for n in range(100))')
57.55613856599666
>>> timeit.timeit('list(map(str, range(100)))')
41.02499054395594
>>> timeit.timeit('list(n for n in range(100) if n % 2 == 0)')
28.473308722954243
>>> timeit.timeit('list(filter(lambda n: n%2 == 0, range(100)))')
42.765857166959904
>>> timeit.timeit('list(str(n) for n in range(100) if n % 2 == 0)')
50.11855198594276
>>> timeit.timeit('list(map(str, filter(lambda n: n % 2 == 0, range(100))))')
62.97385400894564
{% endhighlight %}

