---
layout: post
title: "Python Basics 1: Collections"
author:
    name: Sean Raven
    github: dragon0
description: ""
tags: []
---
{% include JB/setup %}

This article will cover Python's `sequence`, `mapping`, and `set` types.
I will refer to these collectively as `collection` types.
Collections are defined by the special methods `__len__`, `__getitem__`,
`__setitem__`, `__delitem__`, and `__iter__`.
`__len__` is used by the built-in function `len` to get the length/size of a
collection.
`__getitem__`, `__setitem__`, and `__delitem__` are used to define the `[]`
(item access) operator.
`__iter__` is used by the built-in `iter` function to get an `iterator` over the
collection's elements.
Iterators will be covered in a later article.
Note that a `str` is actually a sequence of characters, and follows the same
conventions as other sequence types, such as `list` and `tuple`.

## Lists

Python's `list` type is analogous to C++'s `vector<void*>` or Java's
`List<Object>`.
It can automatically resize itself and the items can be of any type
(including other `list`s).

`list` literals are created using square brackets:

{% highlight pycon %}
>>> my_list = [1, 2, 3]
{% endhighlight %}

As with parentheses, Python ignores whitespace between square brackets:

{% highlight python %}
my_list = [
    1,
    2,
    3,
]
{% endhighlight %}

Also notice that the final item can have a trailing comma.
This allows for easy modification of `list` literals.

{% highlight python %}
my_list = [
    1,
    2,
    3,
    4,
    5,
    'six',
]
{% endhighlight %}

Like `str`, `list` supports indexing, slices, and `len`.

{% highlight pycon %}
>>> my_list[0]
1
>>> my_list[-1]
'six'
>>> my_list[-3:]
[4, 5, 'six']
>>> my_list.find(3)
2
>>> len(my_list)
6
{% endhighlight %}

You can modify the items of a list by assignment.

{% highlight pycon %}
>>> my_list[0] = 'one'
{% endhighlight %}

`list` supports modification using `append`, `insert`, and `pop`.

{% highlight pycon %}
>>> my_list.append('seven')     # add 'seven' to end of list
>>> my_list.insert(0, 'zero')   # place 'zero' at index 0, moving existing items back
>>> my_list.pop(0)              # remove and return the item at index 0
'zero'
>>> my_list.pop()               # remove and return the last item
'seven'
{% endhighlight %}

Note that inserting or popping anywhere except the end of the list takes
linear time; inserting or popping at the end of the list takes constant time.

Since all variables are pointers, assigning a list does pointer assignment.
That is, both names will refer to the same object in memory.

{% highlight pycon %}
>>> my_other_list = my_list         # pointer assignment: both variables refer to the same list!
>>> my_other_list.append('seven')
>>> my_list
['one', 2, 3, 4, 5, 'six', 'seven']
{% endhighlight %}

To make a copy of a list, use slicing.

{% highlight pycon %}
>>> my_copied_list = my_list[:]
>>> my_copied_list.append(8)
>>> my_copied_list
['one', 2, 3, 4, 5, 'six', 'seven', 8]
>>> my_list
['one', 2, 3, 4, 5, 'six', 'seven']
{% endhighlight %}

Note that slice copying performs a *shallow* copy, where both lists share
references to the same objects.

{% highlight pycon %}
>>> my_list_list = [[]]                 # list with a list in it
>>> my_copied_llist = my_list_list[:]
>>> my_copied_llist[0].append(1)        # item 0 is shared
>>> my_copied_llist.append(2)
>>> my_list_list
[[1]]
>>> my_copied_llist
[[1], 2]
{% endhighlight %}

(see [Sequence Types](https://docs.python.org/3/library/stdtypes.html#sequence-types-list-tuple-range)
and [Time Complexity](https://en.wikipedia.org/wiki/Time_complexity)
)

## Tuples

`tuple` is like `list`, except it is immutable (cannot be modified).
`tuple` supports all operations of `list` except ones that modify the `tuple`.
`tuple` is often used like a C/C++ `struct`.

{% highlight pycon %}
>>> point = (3, 5)
>>> point[0]
3
>>> point[-1]
5
>>> point[0] = 4
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'tuple' object does not support item assignment
{% endhighlight %}

### Tuple Packing and Unpacking

The parentheses are only required to prevent ambiguity, and can be omitted.

{% highlight pycon %}
>>> point = 3, 5
{% endhighlight %}

Items in a `tuple` (or any sequence type) can be *unpacked* into variables by
specifying a list of variables on the left-hand side of the assignment operator.

{% highlight pycon %}
>>> x, y = point
{% endhighlight %}

The number of variables must match the length of the sequence (and therefore
must be known when the script is written).

{% highlight pycon %}
>>> x, y, z = point
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: need more than 2 values to unpack
{% endhighlight %}

Packing and unpacking can be combined in one statement.

{% highlight pycon %}
>>> x, y = 3, 5
{% endhighlight %}

This can be used for swapping variables, possibly modifying them in the process.

{% highlight pycon %}
>>> x, y = y, x
>>> a, b = b, a+b
{% endhighlight %}

This can also be used for returning multiple values.

{% highlight pycon %}
>>> def make_point():
...     return 3, 5
... 
>>> point = make_point()
>>> x, y = point
>>> # or
>>> x, y = make_point()
{% endhighlight %}

## Sets

`set` is an unordered collection that contains *unique* values.
An empty `set` can be created using `set()`.
Non-empty `set` literals can be created using curly braces.

{% highlight pycon %}
>>> s = set()
>>> s.add(3)
>>> s.add(3)
>>> s
{3}
{% endhighlight %}

Testing for membership in a `set` is much faster than for `list` or `tuple`.

{% highlight pycon %}
>>> l = [3]
>>> s = {3}
>>> 3 in l # this must compare 3 to every item in l
True
>>> 3 in s # this locates 3 using it's hashcode, and takes constant time
True
{% endhighlight %}

`set` also supports set arithmetic operations
`union`, `intersection`, and `difference`.

{% highlight pycon %}
>>> a = set('abracadabra')
>>> b = set('alacazam')
>>> a      # unique letters in a
{'a', 'r', 'c', 'b', 'd'}
>>> b      # unique letters in b
{'a', 'z', 'c', 'l', 'm'}
>>> a - b  # set difference: letters in a but not in b
{'r', 'd', 'b'}
>>> a & b  # set intersection: letters in both a and b
{'a', 'c'}
>>> a | b  # set union: letters in either a or b
{'a', 'c', 'd', 'l', 'r', 'm', 'b', 'z'}
>>> a ^ b  # symmetric difference: letters in a or b but not both
{'d', 'l', 'r', 'm', 'b', 'z'}
>>> a ^ b == ( a - b ) | ( b - a )
True
{% endhighlight %}

(see [Set Types](https://docs.python.org/3/library/stdtypes.html#set-types-set-frozenset))

## Dictionaries

Dictionaries (`dict`) are Python's *associative array*.
Like `list`, `dict` contains several values that are accessed by index.
Unlike `list`, `dict`'s indices can be any *hashable* object,
and are referred to as `keys`.

An empty `dict` can be created using `dict()` or `{}`.
Non-empty `dict` literals can be created using `key : value` pairs separated by 
commas and enclosed in curly braces.
Like parentheses and square brackets, Python ignores whitespace between curly 
braces, so `dict` literals can span multiple lines for readability.
You can test if a key is in the `dict` using `in`.

{% highlight pycon %}
>>> tel = {'jack': 4098, 'sape': 4193}
>>> tel['guido'] = 4127
>>> tel
{'sape': 4193, 'jack': 4098, 'guido': 4127}
>>> tel['jack']
4098
>>> del tel['sape']
>>> tel['irv'] = 4127
>>> tel
{'irv': 4127, 'jack': 4098, 'guido': 4127}
>>> 'guido' in tel
True
>>> 'jack' not in tel
False
{% endhighlight %}

The `dict()` constructor builds dictionaries directly from sequences of
key-value pairs:

{% highlight pycon %}
>>> dict([('sape', 4139), ('guido', 4127), ('jack', 4098)])
{'sape': 4139, 'jack': 4098, 'guido': 4127}
{% endhighlight %}

When the keys are simple strings, it is sometimes easier to specify pairs using
keyword arguments:

{% highlight pycon %}
>>> dict(sape=4139, guido=4127, jack=4098)
{'sape': 4139, 'jack': 4098, 'guido': 4127}
{% endhighlight %}

You can get a list of the keys in the `dict` using `dict.keys()`.
The return value is an `iterator`, which will be covered later.
For now, just know that you can convert it to a `list` or `tuple` by passing it 
to the appropriate constructor.

{% highlight pycon %}
>>> tuple(tel.keys())
('irv', 'jack', 'guido')
{% endhighlight %}

The `dict` itself is also an iterator over its keys.

{% highlight pycon %}
>>> tuple(tel)
('irv', 'jack', 'guido')
{% endhighlight %}

The keys are returned in arbitrary order. To list the keys in sorted order,
use `sorted`.

{% highlight pycon %}
>>> sorted(tel)
['guido', 'irv', 'jack']
{% endhighlight %}

You can get the values in the dictionary using the `values` method.

{% highlight pycon %}
>>> tuple(tel.values())
(4127, 4098, 4127)
{% endhighlight %}

You can get the key-value pairs using the `items` method.

{% highlight pycon %}
>>> tuple(tel.items())
(('irv', 4127), ('jack', 4098), ('guido', 4127))
{% endhighlight %}

(see [Mapping Types](https://docs.python.org/3/library/stdtypes.html#mapping-types-dict))

### Hashability

Python's `set` and `dict` only work for *hashable* types.
A *hash code* is a number computed from some data, such that the same data 
always has the same hash code, and different data *should* have a different 
hash code.
An object is *hashable* if that object can compute a hash code of its data.
A hashable type implements the method `__hash__`.
An object's hash code can be retrieved using the built-in function `hash`.
Because `list` is mutable, it is not hashable.

{% highlight pycon %}
>>> hash([3, 5])
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unhashable type: 'list'
{% endhighlight %}

Since `tuple` is immutable, tuples are hashable as long as all of the items
are hashable.

{% highlight pycon %}
>>> hash((3, 5))
3713083796996318431
>>> hash(('john', 'doe'))
3003296910174079013
>>> hash((0, []))
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unhashable type: 'list'
{% endhighlight %}

This means that `tuple` can be used as items in a `set` or keys in a `dict`.

{% highlight pycon %}
>>> names = set()
>>> names.add(('john', 'doe')) # add a tuple to the set
>>> points = {}
>>> points[3, 5] = 'floor' # use tuples (3, 5) and (4, 4) as dict keys
>>> points[4, 4] = 'wall'
{% endhighlight %}

#### Implementing Hashable Types

In order for a type to be hashable, it must implement the `__hash__` method.
A general requirement is that two objects which compare equal must have the same
hash code.
You must first implement the `__eq__` method so you know which fields must be
incorporated in the `__hash__` method.

Consider a 2d point class:

{% highlight python %}
class Point:
    def __init__(self, x=0, y=0):
        self.x = x
        self.y = y
    def __eq__(self, other):
        if isinstance(other, Point):
            return self.x == other.x and self.y == other.y
        return NotImplemented
{% endhighlight %}

Since the `__eq__` method compares the `x` and `y` attributes, the `__hash__`
method must also use those attributes.
An easy way to do this is to exclusive-or the hash codes of `x` and `y`:

{% highlight python %}
class Point:
    # ...
    def __hash__(self):
        return hash(self.x) ^ hash(self.y)
{% endhighlight %}

Another option is to pack `x` and `y` into a `tuple` and use the `tuple`'s hash code:

{% highlight python %}
class Point:
    # ...
    def __hash__(self):
        return hash( (self.x, self.y) )
{% endhighlight %}

(see
[Python's requirements on `__hash__`](https://docs.python.org/3/reference/datamodel.html#object.__hash__),
[Java's requirements on hashing](http://docs.oracle.com/javase/7/docs/api/java/lang/Object.html#hashCode()),
[Java's List.hashCode](http://docs.oracle.com/javase/7/docs/api/java/util/List.html#hashCode()),
and [my notes on hashing in Java and C++](https://github.com/SaddlebackCSS/cpbook/wiki/Set)
)

