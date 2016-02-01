---
layout: post
title: "Python Basics 0: Introduction"
author:
    name: Sean Raven
    github: dragon0
description: ""
category: lessons
tags: ["python"]
---
{% include JB/setup %}

This is the beginning of a tutorial series on Python.
Unlike other tutorials, I will focus on certain Python internals,
such as the special protocols which the interpreter expects objects to follow.

## Conventions

I will be using the following conventions in this series.

Lines that start with `>>> ` are to be typed into the Python interactive shell.

{% highlight pycon %}
>>> print('hello, world!')
hello, world!
{% endhighlight %}

Lines that begin with `$` are to be typed into the OS terminal, bash or cmd.
I will be using UNIX conventions unless I specifically mention Windows.

{% highlight sh %}
$ echo hello
hello
{% endhighlight %}

## Installing Python

Python's installer can be downloaded from [the Python website](https://python.org).
If you use Linux or MacOS, you may find that Python is already installed or
available from your package manager.

> #### 2 vs 3
> Python 3 contains changes which are not necessarily backwards-compatible with
> Python 2, so a Python 2 interpreter is still available for download to run
> old code. That doesn't mean that the two are incompatible; it's certainly
> possible to write code that runs under both (especially if Python 2 users are
> using 2.6 or 2.7). However, new projects should be written in Python 3.
>
> This tutorial series will focus on Python 3.

## Running Python

Python can be run from the command-line or in IDLE, a bundled IDE.
There are also plugins available for several major IDEs.

See also: [Python Setup and Usage](https://docs.python.org/3/using/index.html).

### Running From the Command Line

To access your OS command-line:

- On UNIX systems, look for a `terminal` application.
- On Windows, hold down the `Windows` key and press `r`, then type in
  `%COMSPEC%` and click `Run`.


Hopefully, you can run Python simply by typing:

{% highlight sh %}
$ python
{% endhighlight %}

On Windows, you may need to type in the full path to the Python executable:

{% highlight sh %}
$ C:\Python34\python.exe
{% endhighlight %}

On UNIXes, `python` might refer to Python 2, and you must use `python3`
to run Python 3:

{% highlight sh %}
$ python3
{% endhighlight %}

This will run the interpreter in interactive mode, allowing you to enter Python
expressions, which will be evaluated immediately and the results printed.

{% highlight pycon %}
>>> print('hello')
hello
{% endhighlight %}

You can also run a Python script by specifying the script's filename:

{% highlight sh %}
$ python3 myscript.py
{% endhighlight %}

On UNIX, it is common to begin scripts with a `#!` line to tell the kernel
which interpreter to use to run the script.

{% highlight python %}
#!/usr/bin/env python3
print('hello, world!')
{% endhighlight %}

With the `#!` line, you can then mark the file as executable:

{% highlight sh %}
$ chmod +x myscript.py
{% endhighlight %}

And execute it:

{% highlight sh %}
$ ./myscript.py
{% endhighlight %}

### Running in IDLE

IDLE is a Python <abbr title="Integrated Development Environment">IDE</abbr>
written in Python and bundled with the standard Python distribution
(Linux users may need to install a separate `idle` or `idle3` package).
Once Python is installed, you should see an `IDLE` icon in your applications menu.
You can also launch IDLE from the command-line using `idle` (or `idle3`),
or `python -m idlelib`.
Windows users can also find an `idle.bat` file in `C:\Python34\Lib\idlelib`;
you can right-click on this file and select
`Send To > Desktop (Create Shortcut)` to create a shortcut
(once it's running you can pin it to your taskbar or tiles).

Once started, IDLE will display a Python interactive session.
You can press `Ctrl-N` or select `File > New File` to open a text editor.
In the text editor, you can run your script by pressing `F5` or selecting
`Run > Run Module`.
IDLE's shell also provides access to the debugger.
Select `Debug > Debugger` to enable interactive debugging in IDLE.
Interactive debugging causes Python to execute scripts one line at a time,
pausing after each line. This allows you to observe the control flow and watch
how variables are being modified.

You can also press `F1` to access Python's documentation. On Windows, this opens
an offline <abbr title="Compiled HTML">CHM</abbr> file (on other systems, it
just opens the [Python documentation website](https://docs.python.org/3/)
in your browser).

## Objects and Names

Data in Python programs are stored in *objects* which reside in the computer's
memory.
An object's memory location can be bound to a *name* so that the object can be
referred to later.
Use the `=` (assignment) operator to bind a name to an object's memory location.
Several different names can be bound to the same object in memory
(this is the same concept as *pointers* in C or *references* in Java).
A name can only be bound to one object, but identical names can appear in
different *scopes*.
Every object, function, and module has its own scope.

{% highlight pycon %}
>>> x = 5 # bind the name x to an int object
>>> x = 6 # bind the name x to a different int object; the old object is deleted
>>> y = x # y and x refer to the same location in memory
>>> def foo():
...     x = 7 # this name x is in a different scope than the other
... 
{% endhighlight %}

Every object contains a *namespace* to record its attributes.
The names in an object's namespace may be bound to simple values
(like fields in C++/Java) or to functions (aka methods).
Python programmers sometimes use the field/method terminology, but usually
use the term *attribute* to refer to any item in an object's namespace.
An object's attributes can be accessed using the `.` (attribute access)
operator.

{% highlight pycon %}
>>> x.real
6
>>> x.bit_length()
3
{% endhighlight %}

Python employs *reference counting* to determine if an object should be deleted.
Whenever a name is bound to an object, that object's reference count is
increased.
Whenever a name is bound to a new/different object, or goes out of scope, the
(old) object's reference count is decreased.
An object's reference count can also be decreased using the `del` keyword.
When an object's reference count reaches zero, it is deleted.

{% highlight pycon %}
>>> del x # decrements the object's reference count.
          # the object is not actually deleted, because y is still bound to it.
{% endhighlight %}

Python also uses *garbage collection* to detect cycles of objects that refer to
each other, but which are otherwise unused by the rest of the program.

The special value `None` represents no value
(similar to `NULL` in C++ and `null` in Java).

### "Special" Names and Shortcut Functions

The creators of Python reserve all names that begin and end with two underscores.
These names generally have special meaning to the Python interpreter, and this
convention prevents programmers from using names which may be reserved in
future versions of Python.

In the case of object attributes, there are often shortcut functions to access
them.
These are easier to read, and allow the interpreter to provide default behavior
for objects that do not define these attributes.

{% highlight pycon %}
>>> y.__str__() # y might be bound to an object which does not define __str__
'6'
>>> str(y)      # Python can supply a default implementation
'6'
{% endhighlight %}

Much of Python's behavior is defined in terms of these "special" attributes,
allowing programmers to override it.
See also [Special method names](https://docs.python.org/3/reference/datamodel.html#special-method-names).

## Numeric Types

Python has three built-in numeric types: `int`, `float`, and `complex`.
The Python standard library also contains
[decimal](https://docs.python.org/3/library/decimal.html)
and [fraction](https://docs.python.org/3/library/fractions.html)
types.

(see [Numeric Types](https://docs.python.org/3/library/stdtypes.html#numeric-types-int-float-complex))

Arithmetic operators are defined in terms of [these special methods](https://docs.python.org/3/reference/datamodel.html#emulating-numeric-types).

The arithmetic operators are nearly identical to those used in C++ and Java.
Notable exceptions:

- `/` always performs `float` division, even if both operands are `int`
- To perform `int` division, use `//`.
- In Python, the result of `%` has the same sign as the right-hand operand.
  (In C++ and Java, the result has the same sign as the left-hand operand).
- Python has an exponentiation operator, `**`. It works for non-integer and
  negative exponents as well as positive integers.
- Python does *not* have the increment and decrement operators `++` and `--`.
  Instead, use the inline operators (eg `x += 1` or `x -= 1`).


## Strings

Python `str` literals can be enclosed in `'` or `"`.
The only difference is that using one escapes the other.

{% highlight python %}
print('This string can contain "double quotes" without escaping them.')
print("This string can contain 'single quotes' without escaping them.")
print('This string must \'escape\' single quotes.')
{% endhighlight %}

Like C++ and Java, Python strings can contain escape sequences.
Python also supports "raw" strings which do not process escape sequences.
Raw strings are useful for strings that must contain backslashes,
such as regular expressions and Windows filenames.
Raw strings are prefixed with `r`.

{% highlight python %}
print( 'This is a newline: \n')
print(r'This is a backslash followed by n: \n')
print( 'A Windows filename: C:\\Users\\jstudent0\\data.txt')
print(r'A raw Windows filename: C:\Users\jstudent0\data.txt')
print( 'A regex to match a backslash: \\\\')
print(r'A raw regex to match a backslash: \\')
{% endhighlight %}

Python also supports multiline strings. These are enclosed in three `'` or `"`
characters and escape both `'` and `"` inside of them.

{% highlight python %}
print('''This is a multiline string.

It can have ' and " without escaping, but can't have three ''s or "'s in a row.
''')
{% endhighlight %}

Multiline strings are often used for multiline comments.

You can also have a raw multiline string by prefixing it with `r`.

Strings can be concatenated using `+`.

{% highlight pycon %}
>>> s1 = 'hello'
>>> s2 = 'world'
>>> s3 = s1 + ' ' + s2
>>> s3
'hello world'
{% endhighlight %}

Strings can only be concatenated with other strings.
To concatenate other values, pass them to the `str` function
(it's better to use formatting, below).

{% highlight pycon %}
>>> s1 + ' ' + str(47)
'hello 47'
{% endhighlight %}

Strings can be repeated using `*`. The other operand must be an `int`.

{% highlight pycon %}
>>> s1 * 3
'hellohellohello'
>>> 3 * s1
'hellohellohello'
{% endhighlight %}

You can get the length of a `str` using `len`.

{% highlight pycon %}
>>> print(s3, "is", len(s3), "letters long")
hello world is 11 letters long
{% endhighlight %}

(see [Text Sequence Type](https://docs.python.org/3/library/stdtypes.html#text-sequence-type-str))

Any object can be converted to a string using the `repr` or `str` functions.
These functions call the object's `__repr__` or `__str__` methods.
`__str__`'s default implementation calls `__repr__`.
The difference between `repr` and `str` is summarized in the documentation:

[repr](https://docs.python.org/3/reference/datamodel.html#object.__repr__)

> If at all possible, this should look like a valid Python expression that could be used to recreate an object with the same value (given an appropriate environment). If this is not possible, a string of the form <...some useful description...> should be returned.

[str](https://docs.python.org/3/reference/datamodel.html#object.__str__)

> This method differs from `object.__repr__()` in that there is no expectation that `__str__()` return a valid Python expression: a more convenient or concise representation can be used.

### String Formatting

Python's `str` type has a `format` method which can be used to create complexly
formatted string representations of data.
It is similar to C and Java's `printf` and C++'s `iomanip`.
Arguments to the `format` method can define a `__format__` method to produce a
custom representation; otherwise `__str__` is used.

{% highlight python %}
import math
s = "PI to 3 digits: {0:.3f}".format(math.pi)
print(s)
print("Add zeros to ensure three digits after the point: {0:.03f}".format(math.pi))
print("A large number with commas: {0:,}".format(10**7))

expected = 5
actual = 3
print("Display a percent: {0:.03%}".format(actual/expected))

print("A right-aligned field 20 characters wide: {0:>20s}".format("Hello"))
print( "A left-aligned field 20 characters wide: {0:<20s}".format("Hello"))
print(     "A centered field 20 characters wide: {0:^20s}".format("Hello"))
print("A field 20 characters wide, padded with stars: {0:*^20}".format("Hello"))
print("A field 20 characters wide, padded with pluses: {0:+^20}".format("Hello"))

x, y = 3, 5
print("A point: ({0}, {1})".format(x, y))
print("A point with coordinates reversed: ({1}, {0})".format(y, x))

print("A repeated value: {0}{0}{0}".format("Hello"))

print("A hexidecimal number: {0:x}".format(127))
print("A hexidecimal number, with 0x prefix: {0:#x}".format(127))
{% endhighlight %}


(see [Format String Syntax](https://docs.python.org/3/library/string.html#format-string-syntax)
and [Format examples](https://docs.python.org/3/library/string.html#format-examples)
)

### String Processing

To get an individual character, use the subscript operator.

{% highlight pycon %}
>>> s = "Hello"
>>> s[2]
'l'
{% endhighlight %}

Python's subscript operator can also use ranges (called slices).
The range consists of the first index, up to and *not* including the last index.

{% highlight pycon %}
>>> s[2:4]
'll'
>>> s[2:5]
'llo'
{% endhighlight %}

Omitting the first index defaults to 0, omitting the last index defaults to
the end of the string.

{% highlight pycon %}
>>> s[:2]
'He'
>>> s[2:]
'llo'
{% endhighlight %}

Negative indices count from the end.

{% highlight pycon %}
>>> s[-1]
'o'
>>> s[-4:]
'ello'
{% endhighlight %}

You can get the index of a substring using `find`.

{% highlight pycon %}
>>> s.find('e')
1
>>> s.find('lo')
3
>>> s.find('i')
-1
{% endhighlight %}

You can get the *last* index of a substring using `rfind`.

{% highlight pycon %}
>>> s.find('l')
2
>>> s.rfind('l')
3
{% endhighlight %}

You can replace all occurrences of a substring with another using `replace`.

{% highlight pycon %}
>>> '1,2,3'.replace(',', '|')
'1|2|3'
>>> '1,2,3'.replace(',', 'abacadabra')
'1abacadabra2abacadabra3'
{% endhighlight %}

You can divide a string based on a delimiter using `split`.
The result is a `list` of strings.

{% highlight pycon %}
>>> '1,2,3'.split(',')
['1', '2', '3']
{% endhighlight %}

Without a parameter, `split` splits on whitespace.

{% highlight pycon %}
>>> '1     2\t3'.split()
['1', '2', '3']
{% endhighlight %}

If you have a multiline string, you can get a list of the individual lines
using `splitlines`.

{% highlight pycon %}
>>> 'ab c\n\nde fg\rkl\r\n'.splitlines()
['ab c', '', 'de fg', 'kl']
{% endhighlight %}

You can combine the elements of a sequence on a delimiter using `join`.

{% highlight pycon %}
>>> ', '.join([1, 2, 3])
'1, 2, 3'
{% endhighlight %}

Other useful `str` methods include:

- `upper`
- `lower`
- `startswith`
- `endswith`
- `isalnum`
- `isalpha`
- `isdigit`
- `isspace`

## Boolean Conversions

Python uses the following convention for converting objects to booleans:

> None, False, any numeric 0, and any empty sequence are considered `False`.
> Everything else is considered `True`.

(see [Truth Value Testing](https://docs.python.org/3/library/stdtypes.html#truth))

An object can be converted to a boolean using the `bool` function.
Internally, Python applies the `bool` function to the expression used in the
`if` and `while` statements.

`bool` works as follows:

- If the given object defines a `__bool__` method, it is called.
  The result must be `True` or `False`.
- If the object does not define `__bool__` but defines `__len__`, then `__len__`
  is called. If it returns `0`, the object is considered `False`.
- If the object defines neither `__bool__` nor `__len__`, then it is considered 
  `True`.

## Boolean Operators

Python's boolean operators are implemented using the keywords `and`, `or`, and `not`.

`and` and `or` actually return the *last object evaluated*.
This can be used to give default values to variables:

{% highlight python %}
if name == '':
    name = 'fred'
# can be rewritten as
name = name or 'fred'
{% endhighlight %}

Note that the two examples are not *exactly* identical.
The `if` statement only compares `name` to `''`, while the `or` expression
will reassign `name` if it has any value that is considered `False`.

## Relational Operators

Python's relational operators are nearly identical to those used in C++ and Java.
Notable exceptions:

- Python's operators can be chained (eg `3 < x < 7` is the same as `3 < x and x < 7`)
- Python's operators are defined for sequence types to perform lexicographic comparisons.
- Python has `is` and `is not` operators, which perform pointer comparison
  (ie they check whether two variables point to the same object)
- Python has sequence operators `in` and `not in` (defined by `__contains__`)
  to check whether an item is in a sequence or not (eg `"foo" in mystr`)
- To prevent confusion with the equality operator `==`, Python does not allow
  the assignment operator `=` in the expression for `if`, `elif`, and `while`
  statements.


The relational operators are defined by the special methods `__lt__` (<),
`__le__` (<=), `__eq__` (==), `__ne__` (!=), `__gt__` (>), and `__ge__` (>=).


## Conditional Operator

Python's conditional operator uses the keywords `if` and `else`.
Note that the then-value comes *before* the condition.

{% highlight python %}
sign = '+' if val >= 0 else '-'
{% endhighlight %}

## Statements and Whitespace

Statements are terminated by newlines.
You can use semicolons, but they are only necessary if there are multiple
statements on the same line.

Control-flow statements follow the pattern `{keyword} [expression] ":"`.
The body of the statement is indented using the following rules:

- The first line after the statement sets the indentation level for that block.
- Every subsequent line that is indented to the same level is part of the block.
- An unindented line marks the end of the block. The interpreter will check the
  indentation of enclosing blocks to determine which block the unindented line
  belongs to.


{% highlight python %}
x = 5
while x > 0:
    print(x)
    x = x - 1
print("done")
{% endhighlight %}

Between parentheses, all whitespace is ignored.
If you want a statement to span multiple lines, enclose it in parentheses.

{% highlight python %}
print(
    "the first",
    "the second",
    "the third")

d = (
          (x2 - x1) ** 2
        + (y2 - y1) ** 2
    ) ** 0.5
{% endhighlight %}

Python checks for whitespace errors **before** executing any code, so
any incorrect indentation will be reported immediately when attempting to run.


## The `while` Statement

Python's `while` statement works the same as in C++ and Java.
The condition expression is converted to a `bool` using the rules described above.

{% highlight python %}
num = int(input("Enter an integer - 0 to exit: "))
numSum = 0
while num: # evaluates as num != 0
    numSum += num;
    num = int(input("Enter an integer - 0 to exit: "))
print("The sum of the integers is", numSum)
{% endhighlight %}

Python's `while` statement can also have an `else` clause.
The `else` clause is only executed if the loop terminates due to its condition
becoming `False` (ie it does not execute if the loop terminates due to a `break`
statement).

{% highlight python %}
def search(start, target):
    visited = set()
    queue = [start]
    while queue: # loop while queue is not empty
        u = queue.pop()
        visited.add(u)
        if u == target:
            break # target found
        for v in children(u):
            if v not in visited:
                queue.append(v)
    else: # queue is empty and target was not found
        print("graph exhausted, target not found")
        return None
    print("found target")
    return u
{% endhighlight %}

**Python does not have a do-while statement**

## The `if` Statement

Python's selection statement is `if-elif-else`.

{% highlight python %}
grade = input("Enter your grade (A - F): ")

if grade == 'A':
    print("Excellent")
elif grade == 'B':
    print("Good")
else:
    print("Work harder")

print("Done")
{% endhighlight %}

The condition expression for `if` and `elif` can evaluate to any object;
Python will convert the result to `bool`.

Python does not have a switch statement.
Its effect can be simulated using the `in` operator.

{% highlight python %}
if classCode in ('F', 'f'):
    print("you are a freshman")
elif classCode in ('S', 's'):
    print("you are a sophmore")
#...
{% endhighlight %}

## The `import` Statement

Python programs can be divided into *modules*: independent, reusable Python files.
Modules can be grouped into *packages*. A package is a folder containing a file
named `__init__.py`. A package can contain modules and subpackages.

Packages and modules can be loaded using the `import` statement.
The Python interpreter has a search path where it searches for packages and
modules. The search path can be accessed using `sys.path`:

{% highlight pycon %}
>>> import sys
>>> sys.path
['', '/home/sean/SaddlebackCSS/SaddlebackCSS.github.io', '/usr/lib/python3.4', '/usr/lib/python3.4/plat-x86_64-linux-gnu', '/usr/lib/python3.4/lib-dynload', '/usr/local/lib/python3.4/dist-packages', '/usr/lib/python3/dist-packages']
{% endhighlight %}

Python ships with [lots of built-in modules](https://docs.python.org/3/library/index.html).
There are also [many third-party modules](https://pypi.python.org/pypi) available.

The `import` statement has an alternate form: `from`.
The difference is that `import` will create a namespace for the imported module
while `from` will bring names in the module into the current namespace.

{% highlight pycon %}
>>> import math
>>> math.sin(math.radians(45)) # sine of 45 degrees (sin expects radians)
0.7071067811865475
>>> from math import sin, radians
>>> sin(radians(45))
0.7071067811865475
{% endhighlight %}

Both `import` and `from` can use the `as` keyword to assign an alias to the
imported module.

{% highlight pycon %}
>>> import math as m
>>> m.sin(m.radians(45))
0.7071067811865475
{% endhighlight %}

## Virtualenv, PIP, and Setuptools

Due to the large number of available Python libraries, it is often necessary to
install libraries which may conflict with each other, or install different
versions of the same library.
Python includes the [venv](https://docs.python.org/3/library/venv.html) module
to help keep conflicting libraries and versions from interfering with each other.

According to the [documentation](https://docs.python.org/3/library/venv.html#venv-def):

> A virtual environment (also called a venv) is a Python environment such that 
> the Python interpreter, libraries and scripts installed into it are isolated 
> from those installed in other virtual environments, and (by default) any 
> libraries installed in a “system” Python, i.e. one which is installed as 
> part of your operating system. 

You can create a venv using Python's `venv` module:

{% highlight sh %}
$ python -m venv myenv
{% endhighlight %}

The `-m venv` tells Python to execute the `venv` module. `myenv` is the name of
the venv to create.
Once complete, there will be a new folder called `myenv`.
This folder will contain a copy of the Python interpreter and an `activate` script.

Run the `activate` script in your OS shell:

UNIX:

{% highlight sh %}
$ source myenv/bin/activate
{% endhighlight %}

Windows:

{% highlight sh %}
$ myenv\Scripts\activate.bat
{% endhighlight %}

When you run the `activate` script, your command-line shell will be configured
to run the venv's Python interpreter when you type `python`.
You can also install packages into the venv using `pip` and `setuptools`.

You can deactivate a venv by typing `deactivate` in the command-line.

Pip is used to download and install Python packages from the central
[Python Package Index](https://pypi.python.org/pypi).
With your venv active, installing via `pip` will install the packages into the
venv's folder, so that they do not interfere with any system packages or
packages in other venvs.

{% highlight sh %}
$ pip install markdown
$ echo '**hello world**' | python -m markdown
<p><strong>hello world</strong></p>
{% endhighlight %}

Some packages are not available via `pip`. You must download and extract the
package yourself and install it using `setuptools`.
The package will contain a `setup.py` file. You can install the package using:

{% highlight sh %}
$ python setup.py install
{% endhighlight %}

Since a venv is contained entirely within its folder, you can delete a venv
by deleting its folder. Nothing else on your system will be affected.

