---
layout: post
title: Python Jumpstart
author:
    name: Sean Raven
    github: dragon0
date: 2016-09-12 18:30:00 -07:00
description: Python Jumpstart
tags: ["python"]
---
{% include JB/setup %}

This is a simple syntax reference for Python, intended for people with prior programming experience.
If you know basic control flow, functions, and file i/o in another language, try using this to jump into Python.
There is a more comprehensive quick reference at [Learn X in Y minutes](https://learnxinyminutes.com/docs/python3/)
(which also has quick references for many other languages).

[Install Python](https://python.org),
run Idle (Mac and Linux users may need to open a terminal and run `idle3` or `python3 -m idlelib`),
press `CTRL-N` or `File > New File` to start writing a script,
save your script as a `.py` file,
and press `F5` or `Run > Run Module` to run it.
You can also run the script from the command-line using `python myscript.py` or `python3 myscript.py`.
You can access Python's documentation from within Idle by pressing `F1`.

---

- [Basics](#basics)
- [Control Flow](#control-flow)
- [Console I/O](#console-io)
- [File I/O](#file-io)
- [Lists](#lists)
- [Dictionaries](#dictionaries)
- [Classes](#classes)
- [Doctests](#doctests)


## Basics

```python
name = 'fred' # can use ' or "
print(name)
print(type(name))
# help and dir are VERY useful when used in the shell
print(dir(str))
help(str)

quote = "he's dead, Jim" # using " auto-escapes '
quote = 'he\'s dead, Jim' # you can still use \ to escape ' or "
# standard Python style is to use ' unless your string contains ' but not "
quote = '"he\'s dead, Jim," said McCoy'

multiline = '''This is a multiline string.

It starts and ends with triple ' or ".

It can span multiple lines, and quotes are automatically escaped.

It's also sometimes used for block comments.
'''

x = 5
y = 2.7
print(x / y)  # / is always floating-point division
print(x // y) # use // if you really want int division
print(int(x // y)) # if you REALLY want an int, you have to cast it to an int
print(x * x)
print(x ** x) # exponentiation operator

# if Python thinks something doesn't make sense, it raises an exception
try:
    print(5 + '5')
except TypeError:
    pass

print(5 + int('5'))
print(5 + float('5.5'))

print(str(5) + '5')

```

## Control Flow

```python
if 0 < x <= 10: # comparison operators can be chained like in math
    print(x)
elif y < x and x < 10: # boolean operators are keywords
    print(y, x)
else:
    print(y)

a, b = 0, 1 # multiple assignment (actually, tuple packing and unpacking)
while b < 10:
    print(b, end=', ')
    a, b = b, a+b
print()

thelist = [1, 2, 'three', 4.0]

for item in thelist:
    print(item)

# use len() to get the length of stuff
for index in range(len(thelist)):
    print(index, thelist[index])

for index, item in enumerate(thelist):
    print(index, item)

while thelist:
    last_item = thelist.pop()

# a function
# the string is called a docstring and is used for documentation
# you can have segments that look like the Python shell and use the
# doctest module to test them
def fib(n):
    """
    Returns the nth fibonacci number.

    >>> fib(10)
    89
    """
    a, b = 0, 1
    c = 0
    while c < n:
        a, b = b, a+b
        c += 1
    return b

z = fib(10)
print(z)
help(fib)

```

## Console I/O

```python
name = input('What is your name? ')

got_age = False
while not got_age:
    age = input('What is your age?  ') # input returns a str
    try:
        age = int(age) # parse it as an int
    except ValueError:
        print("That's not a valid integer")
    else:
        got_age = True
    finally:
        print() # I couldn't think of anything else to demo finally with

print("Your name is", name, "and your age is", age)
# str.format is very useful
# it's like Python's iomanip
print("Your name is {name} and your age is {age}".format(name=name, age=age))
print("Your name is {name} and your age is {age}".format_map(vars()))
print(name, age, sep=", ", end="!\n")

```

## File I/O

```python
with open('input_file.txt') as input_file: # with ensures the file gets closed
    lines = 0
    for line in input_file:
        lines += 1

print("the input file has", lines, "lines")

# the 'w' means open in write mode
with open('output_file.txt', 'w') as output_file:
    print("the input file has", lines, "lines", file=output_file)
    output_file.write("(isn't that great?)\n")

```

## Lists

```python
# items are just pointers (can be any type)
the_list = [1, 2, 3.14, 'four', [5, 6, 7]]
print(the_list[0])
print(the_list[2])
print(the_list[-1]) # negatives count from the end
print(the_list[-1][1])
print(the_list[1:4]) # "slice" returns a sublist
print(the_list[1:])
print(the_list[:-1])
print(the_list[:])

the_list.append(9001)
the_list[0] = '1!'
first_value = the_list.pop(0) # WARNING: moves all items over (linear time)
last_value = the_list.pop()   # doesn't need to move anything (constant time)
print(the_list)

also_the_list = the_list # pointer/reference assignment
the_list.append(5)
print(the_list)
print(also_the_list)
print(the_list == also_the_list)
print(the_list is also_the_list) # is does a pointer comparison

not_the_list = the_list[:] # the slice returns a copy
print(the_list == not_the_list)
print(the_list is not_the_list)
the_list.append(66.6)
print(the_list)
print(not_the_list)

```

## Dictionaries

```python
empty_dict = {}

d = {'key': 'value', 'name': name}
print(d['key'])
d['key'] = 'a different value'
d['non-existant key'] = 'gets created'
print(d)

value = d['key']
try:
    value = d['this key does not exist']
except KeyError:
    print('the key does not exist')

value = d.get('this key does not exist', 'so this is returned instead')
print(value)

print('name' in d)

for key in d:
    print(key, d[key])

for key, value in d.items():
    print(key, value)

```

## Classes

```python
class Vector2D:
    '''
    An example class
    '''
    def __init__(self, x, y):
        '''
        This is the constructor.

        (actually, it's the initializer)
        '''
        self._x = x # members with leading underscores are assumed to be private
        self._y = y

    def magnitude(self): # all methods need a self parameter
        '''
        returns the magnitude.

        >>> v = Vector2D(3, 4)
        >>> v.magnitude()
        5.0
        '''
        return (self._x ** 2 + self._y ** 2) ** 0.5

class Vector3D(Vector2D):
    '''
    A subclass
    '''
    def __init__(self, x, y, z):
        # call superclass initializer
        # (it doesn't get called automatically)
        super().__init__(x, y)
        self._z = z

    def magnitude(self): # nothing special to override
        '''
        >>> v = Vector3D(2, 3, 6)
        >>> v.magnitude()
        7.0
        '''
        return (self._x ** 2 + self._y ** 2 + self._z ** 2) ** 0.5

myVector2 = Vector2D(3, 4)
print(myVector2.magnitude())
myVector3 = Vector3D(2, 3, 6)
print(myVector3.magnitude())

```

## Doctests

[see here](https://docs.python.org/3/library/doctest.html)

```python
if __name__ == '__main__':
    import doctest
    doctest.testmod(verbose=True)

```
