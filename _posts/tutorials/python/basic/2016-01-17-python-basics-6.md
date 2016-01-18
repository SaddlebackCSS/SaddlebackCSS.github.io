---
layout: post
title: "Python Basics 6: Error Handling"
author:
    name: Sean Raven
    github: dragon0
description: ""
tags: []
---
{% include JB/setup %}

## Exceptions

Exceptions are used to report errors or other exceptional conditions.
If an exceptions is not handled, it will print a *traceback* indicating what
kind of exception occurred and where it originated in the code.

Exceptions are reported with the `raise` statement.
An error message can be supplied in parentheses.

{% highlight pycon %}
>>> raise Exception
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
Exception
>>> raise Exception("this is the message")
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
Exception: this is the message
{% endhighlight %}

There are [many kinds](https://docs.python.org/3/library/exceptions.html)
of exceptions.
They all derive from `BaseException` and most derive from `Exception`.
Some of the more common ones are:

- `TypeError`: raised by functions to indicate that an argument is of incorrect
  type, such as passing a `str` where an `int` or `float` was expected.
- `ValueError`: raised by functions to indicate that an argument is of the
  correct type but contains an invalid value, such as passing a negative
  number to a function that requires a nonnegative value.
- `KeyError`: raised when a `dict` key does not exist.
- `IndexError`: raised when an index to a sequence is out of range.


It is common to write functions to check the type and value of arguments and
raise `TypeError` or `ValueError` before attempting to manipulate them.
Here is an example `factorial` function which verifies the type and value of its
parameter:

{% highlight python %}
def factorial(n):
    """
    Compute the factorial of n (n!).

    :param n: must be int
    :returns: n! (i.e. n factorial)
    :raises TypeError: if n is not an int
    :raises ValueError: if n is negative
    """
    if not isinstance(n, int):
        raise TypeError("n must be int, got {}".format(n.__class__.__qualname__))
    if n < 0:
        raise ValueError("n must not be negative")
    if n == 0:
        return 1
    else:
        return n * factorial(n-1)
{% endhighlight %}

Note that Python favors
[duck typing](https://en.wikipedia.org/wiki/Duck_typing), which means
identifying an object's "type" by its abilities, not its actual type (class).
Using duck typing, the above `factorial` function would not explicitly use the
`isinstance` function, but assume that `n` "acts" like an `int`, i.e. it defines
the comparison and arithmetic operators as used by `factorial`.

When implementing your own library, it is common to define your own exceptions
which are specific to your library.
Exceptions are classes which inherit from `Exception`.
Since the only important part of an exception's definition is its type, and all
of the required logic is implemented in `Exception`, the custom exception's body
can consist of only a docstring or the
[pass](https://docs.python.org/3/reference/simple_stmts.html#the-pass-statement)
statement.

{% highlight python %}
class MyCustomBaseException(Exception):
    pass

class MyCustomSpecificException(MyCustomBaseException):
    """
    Raised when ...
    """
{% endhighlight %}

## The `try` Statement

The `try` statement is used to handle exceptions.
Any code which might produce an exception that you want to handle must go in a
`try` block.
The `try` block must be followed by either an `except` block or a `finally`
block.
The `except` block is given the type of exception that you want it to handle.
You can bind the exception object to a name using the `as` keyword.
There can be multiple `except` blocks to check for different types of exceptions.
If an exception is raised by code in the `try` block, the exception object's
type is checked against the types of the following `except` blocks; the first
matching block is executed.
This means that you must put `except` blocks for more specific types of
exceptions *before* any `except` blocks for more general types of exceptions;
if you put the more general type first then the more specific block won't be
executed.
The last `except` block can omit the exception type; this is used to catch *all*
exceptions.
Within an `except` block, the `raise` statement without an argument will
re-raise the exception; this is used to add cleanup code while still propagating
the exception.
After all `except` blocks, there can be an `else` block, which is executed only
if the `try` block completed without exceptions.
A `finally` block is can be placed at the end of the `try` statement.
The `finally` block is *always* executed, regardless of whether an exception
occurred or not.

{% highlight python %}
x = int(input("Enter a number: "))
while True:
    try:
        y = factorial(x)
    except ValueError:
        print("You must enter a positive number or 0")
    except TypeError as e:
        print(str(e))
    except:
        print("Something unexpected occurred!")
        raise
    else:
        print("{x}! = {y}".format(x=x, y=y))
    finally:
        x = int(input("Enter another number: "))
{% endhighlight %}

See [the try statement](https://docs.python.org/3/reference/compound_stmts.html#the-try-statement).

## The `with` Statement

The `with` statement is used to wrap a block with methods defined by a
[context manager](https://docs.python.org/3/reference/datamodel.html#context-managers).
A context manager is defined by the methods `__enter__` and `__exit__`.
The expression given to `with` must evaluate to a context manager.
The context manager's `__enter__` method is called and the return value is bound
to the name following `as`.
When the `with` block terminates, the context manager's `__exit__` method is
called.
The `__exit__` method is *always* called, whether the `with` block terminated
normally or due to an unhandled exception.

{% highlight python %}
with expr as c:
    #...

# is roughly equal to 

manager = expr
c = manager.__enter__()
try:
    #...
finally:
    manager.__exit__()
    del manager

{% endhighlight %}

Python's built-in `file` type is a context manager whose `__enter__` returns
itself and `__exit__` closes the file.
Using files in a `with` statement ensures that they are closed properly.

{% highlight python %}
with open("somefile.txt") as f:
    # if an error occurs here, the file will be closed
    data = f.read()

# at this point, the file is closed
print(data)
{% endhighlight %}

Without the `with` statement, the above would have to be written like this:

{% highlight python %}
f = open("somefile.txt")
try:
    data = f.read()
finally:
    f.close()
print(data)
{% endhighlight %}

See [PEP 343](https://www.python.org/dev/peps/pep-0343/).
