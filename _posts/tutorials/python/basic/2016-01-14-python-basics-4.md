---
layout: post
title: "Python Basics 4: Docstrings, pydoc, doctest, and Function Annotations"
author:
    name: Sean Raven
    github: dragon0
description: ""
category: lessons
tags: ["python"]
---
{% include JB/setup %}

## Docstrings

When the first statement in a function (or class or module) is a string literal,
it is referred to as a `docstring` and stored in the attribute `__doc__`.

{% highlight pycon %}
>>> def foo():
...     'this is the docstring'
... 
>>> foo.__doc__
'this is the docstring'
{% endhighlight %}

When running the interpreter in interactive mode, the
[help](https://docs.python.org/3/library/functions.html#help) function will
display the docstring in a more readable format.

Triple-quoted strings are often used for docstrings so that they can span
multiple lines.
Multi-line docstrings often start with a short description of the function,
followed by a blank line and detailed descriptions of the parameters and return
value.
Standard conventions for docstrings are described in
[PEP 257](https://www.python.org/dev/peps/pep-0257/).
A more structured format is presented in
[PEP 287](https://www.python.org/dev/peps/pep-0287/).
A list of alternative formats is on
[StackOverflow](http://stackoverflow.com/a/24385103).

{% highlight pycon %}
>>> def hypotenuse(x, y):
...     """Computes the distance from the origin to the point (x, y)
... 
...     :param x: the point's x-coordinate
...     :param y: the point's y-coordinate
...     :return: number. the distance from (0, 0) to the point (x, y)
...     """
...     return (x**2 + y**2) ** 0.5
... 
>>> hypotenuse(3, 4)
5.0
{% endhighlight %}

## The pydoc Module

The [pydoc](https://docs.python.org/3/library/pydoc.html) module is used to
view or generate HTML documentation from the docstrings in a script or module.
It can be executed using the `pydoc` command (or, equivalently,
`python -m pydoc`), followed by the name of the module or script file.
If the script is in the current directory, use either its module name (ie its
file name without the `.py` extension) or prefix its filename with `./` (or `.\`
on Windows).
By default, `pydoc` will display the documentation on the command-line.
To generate HTML files, use the `-w` option.

## The doctest Module

The [doctest](https://docs.python.org/3/library/doctest.html) module allows for
automated testing of functions based on examples in the docstrings.

When run, the below script will execute the tests in the docstring for
`hypotenuse` and display a summary of the results.
The final three lines can be placed at the end of any Python source file to
run its doctests.

{% highlight python %}
def hypotenuse(x, y):
    """Computes the distance from the origin to the point (x, y)

    :param x: the point's x-coordinate
    :param y: the point's y-coordinate
    :return: number. the distance from (0, 0) to the point (x, y)

    >>> hypotenuse(3, 4)
    5.0
    >>> hypotenuse(5, 12)
    13.0
    """
    return (x**2 + y**2) ** 0.5

if __name__ == "__main__":
    import doctest
    doctest.testmod(verbose=True)
{% endhighlight %}

You can also maintain a file containing many tests and use `doctest` to check
them.
See the doctest module's
[documentation](https://docs.python.org/3/library/doctest.html) for details.

A useful exercise is to try to write the doctests before writing the function.
This level of [black-box testing](https://en.wikipedia.org/wiki/Black-box_testing)
forces you to focus on *what* the function is supposed to do, instead of *how*
it does it.
Tests which check *how* the function reaches its value
(aka [white-box testing](https://en.wikipedia.org/wiki/White-box_testing))
can be added later.

## Function Annotations

Function annotations are optional, arbitrary metadata attached to a function's
parameters and return value.
They are not used by the Python interpreter, but may be used by other tools
(such as static analysis tools), or just to document the code.
Function annotations can be any valid Python expression and are evaluated when
the function definition is read.
The values of the annotations are stored in the function's `__annotations__`
attribute, which is a `dict` whose keys are the names of the parameters or
"return" for the return annotation.

{% highlight python %}
def hypotenuse(x: float, y: float) -> float:
    return (x**2 + y**2) ** 0.5
{% endhighlight %}

The [typing](https://docs.python.org/3/library/typing.html) module (in Python
3.5 and later) contains definitions for abstract type hints, which may be
used in annotations to help static type analysis tools.

See also:

- [PEP 3107 (annotations)](https://www.python.org/dev/peps/pep-3107/)
- [PEP 484 (type hints)](https://www.python.org/dev/peps/pep-0484/)

