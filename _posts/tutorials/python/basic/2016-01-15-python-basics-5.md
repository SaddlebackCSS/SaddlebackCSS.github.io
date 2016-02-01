---
layout: post
title: "Python Basics 5: Scripts, Modules, and Packages"
author:
    name: Sean Raven
    github: dragon0
description: ""
tags: ["python"]
---
{% include JB/setup %}

## Scripts

Python code can be stored in a text file and later executed by the interpreter.
Such files are referred to as *scripts*.

Note that scripts must be stored in *plain* text.
Word processors are not suitable for writing Python code, because they will
insert extra formatting information which the interpreter will not understand.

Unlike the interactive mode, a script will not display the results of its execution.
The [print](https://docs.python.org/3/library/functions.html#print) function
must be used instead.

Scripts can be executed from the command line by passing the script name to the
`python` (or `python3`) command.

{% highlight sh %}
$ python myscript.py
{% endhighlight %}

Some modules (see below) can also be executed as scripts.
These can be executed using the `-m` option, which will cause the interpreter
to search for the module in the same way as the `import` statement.

{% highlight sh %}
$ python -m some_module
{% endhighlight %}

There are ways to make your script executable by itself:

- On Windows, when Python is installed it registers itself to open files ending
  with the `.py` extension.
  Simply write the file name on the command-line to run it.
  You can also double-click on a `.py` file to run it.
  This will open a `conhost.exe` window to display output.
  Note that the window will close immediately when the program finishes, so
  you will not be able to read any output or error messages from your program.
- On UNIX (Linux and Mac OS), the script must start with the line
  `#!/usr/bin/env python2` or `#!/usr/bin/env python3`.
  The script must then be marked as executable using the command
  `chmod +x myscript.py`.
  Note that the `#!` line tells the OS which interpreter to use, **not** the
  filename extension, so the extension can be anything (or omitted).
- To ensure compatibility for your users, scripts should have **both** the
  `.py` extension and the `#!` line, regardless of which OS you use.


A script can read from and write to the terminal using the
[input](https://docs.python.org/3/library/functions.html#input)
and
[print](https://docs.python.org/3/library/functions.html#print)
functions.
It can also access file objects representing the terminal through the
[sys](https://docs.python.org/3/library/sys.html) module, via `sys.stdin`,
`sys.stdout`, and `sys.stderr`.
A script can access its command-line arguments using `sys.argv` or the
[argparse](https://docs.python.org/3/library/argparse.html) module.


## Modules

A *module* is a Python source file which contains only function (and class)
definitions.
A module is not intended to be executed as a script, but used by scripts (and
other modules) using the `import` statement.

For example, the following code may be stored in a file called `vecutils.py`

{% highlight python %}
def add_vectors(vec1, vec2):
    x1, y1 = vec1
    x2, y2 = vec2
    return x1 + x2, y1 + y2

def mul_vector(vec, m):
    x, y = vec
    return x * m, y * m

def mul_dot(vec1, vec2):
    x1, y1 = vec1
    x2, y2 = vec2
    return (x1 * x2) + (y1 * y2)
{% endhighlight %}

These functions can then be used by a script (or another module) by importing it:

{% highlight pycon %}
>>> import vecutils
>>> vecutils.mul_vector( (3,5), 7 )
(21, 35)
{% endhighlight %}

You can read more about importing modules in the
[Python tutorial](https://docs.python.org/3/tutorial/modules.html).
A thorough discussion on importing modules can be found in the documentation on
[the import system](https://docs.python.org/3/reference/import.html).
Note that module filenames must end with the `.py` extension.

Sometimes it is useful for a single source file to behave as both a module and
a script; i.e. it contains functions that can be used by other modules and also
does something useful when run by itself.
To support this, every module has a global name `__name__`.
It contains the name used to import the module, or the value `'__main__'` if it
is being run as a script.
A module which also acts as a script can check whether it is being imported
or not by checking the value of `__name__`.
A common pattern is for a script to begin with function definitions, then have
script code at the end, guarded by `if __name__ == '__main__'`:

{% highlight python %}
# function definitions here

if __name__ == '__main__':
    # script code here
{% endhighlight %}

Another common pattern is to have a `main` function near the beginning of the
module, and call it at the end:

{% highlight python %}
def main():
    # script code here

# function definitions here

if __name__ == '__main__': main()
{% endhighlight %}

Even if a module is not intended to run as a script, it can still be useful to
end with an `if __name__ == '__main__':` block (e.g. to run
[doctests](https://docs.python.org/3/library/doctest.html)
or
[unit tests](https://docs.python.org/3/library/unittest.html)).
The above `vecutils.py` module might be written like this:


{% highlight python %}
def add_vectors(vec1, vec2):
    """Add the vectors together

    :param vec1: A 2-tuple of the form (x, y)
    :param vec2: A 2-tuple of the form (x, y)
    :returns: 2-tuple containing the sum

    >>> add_vectors((3, 5), (4, 7))
    (7, 12)
    >>> add_vectors((123321, 89926), (23454, 35678923))
    (146775, 35768849)
    """
    x1, y1 = vec1
    x2, y2 = vec2
    return x1 + x2, y1 + y2

def mul_vector(vec, m):
    """Multiply a vector by a scalar

    :param vec: A 2-tuple of the form (x, y)
    :param m: Multilplier (number)
    :returns: 2-tuple containing the product

    >>> mul_vector((3, 5), 8)
    (24, 40)
    >>> mul_vector((35536, 5001), 800000)
    (28428800000, 4000800000)
    """
    x, y = vec
    return x * m, y * m

def mul_dot(vec1, vec2):
    """Compute the dot product of two vectors

    :param vec1: A 2-tuple of the form (x, y)
    :param vec2: A 2-tuple of the form (x, y)
    :returns: single number containing the product

    >>> mul_dot((3, 5), (7, 9))
    66
    >>> mul_dot((3600, 5001), (78899, 98765))
    777960165
    """
    x1, y1 = vec1
    x2, y2 = vec2
    return (x1 * x2) + (y1 * y2)

if __name__ == "__main__":
    # if run as a script, check the tests in the docstrings
    import doctest
    doctest.testmod()
{% endhighlight %}

## Packages

Large projects may need to be divided into many modules.
A *package* is a special kind of module which can contain submodules.
The easiest way to create a package is to make a folder which contains an
`__init__.py` file.
The `__init__.py` file can be empty or contain initialization code.
Submodules are ordinary Python modules contained in this folder.
You can read more about packages in the
[Python tutorial](https://docs.python.org/3/tutorial/modules.html)
and [import system](https://docs.python.org/3/reference/import.html).
You can learn about creating and distributing your modules and packages in
[Distributing Python Modules](https://docs.python.org/3/distributing/index.html).

