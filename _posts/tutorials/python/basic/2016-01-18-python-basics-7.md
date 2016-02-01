---
layout: post
title: "Python Basics 7: Classes, Operators, Decorators, Properties, Descriptors, and Metaclasses"
author:
    name: Sean Raven
    github: dragon0
description: ""
tags: ["python"]
---
{% include JB/setup %}

## Classes

A *class* is an abstraction for a user-defined datatype with its own internal
values (fields) and behaviors (methods).
Objects are *instances* of classes.
All objects which are instances of the same class will have the same fields and
methods (Python uses the term *attribute* to refer to fields and methods).
In Python, individual objects can have additional attributes added to their
internal namespaces, but all instances will have at least the attributes defined
by the class.

Classes are created using the keyword `class` followed by the class's name and
body.
The class body is executed in its own namespace.
Any names defined in this namespace become attributes of the class.

{% highlight pycon %}
>>> class Point:
...     x = y = 0
... 
>>> Point.x
0
>>> Point.y
0
>>> Point.z
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: type object 'Point' has no attribute 'z'
{% endhighlight %}

Instances are created by calling the class as though it were a function.

{% highlight pycon %}
>>> p = Point()
>>> p.x = 5
>>> p.x
5
>>> p.y
0
>>> Point.x
0
>>> Point.y
0
{% endhighlight %}

When attempting to access a particular attribute of an object, the object's
namespace is searched for that attribute.
If the attribute is not found, the namespaces of the object's class and all
superclasses are searched.
The exact algorithm is described by the
[Method Resolution Order](https://www.python.org/download/releases/2.3/mro/).
A metaclass can define an
[mro](https://docs.python.org/3/library/stdtypes.html#class.mro) method to
override the default order.

Methods are implemented by functions in a class body.
The instance is passed as the first argument so the function can access the
instance's namespace.
This can be done explicitly (by accessing the function through the class)
or implicitly (by accessing the function through the instance).
By convention, the first parameter of a method is called `self`.

{% highlight pycon %}
>>> class Point:
...     x = y = 0
...     def magnitude(self):
...         return (self.x ** 2 + self.y ** 2) ** 0.5
... 
>>> p = Point()
>>> p.x = 5
>>> Point.magnitude(p) # access magnitude through Point and pass p
5.0
>>> p.magnitude() # access magnitude through p. p is automatically passed as self
5.0
>>> p.y = 7
>>> p.magnitude()
8.602325267042627
{% endhighlight %}

The special method `__init__` is used to initialize the new instance.
The arguments to the class are passed to `__init__`.

{% highlight pycon %}
>>> class Point:
...     def __init__(self, x=0, y=0):
...         self.x = x
...         self.y = y
... 
>>> p = Point(3, 5)
>>> p.x
3
>>> p.y
5
{% endhighlight %}

You may notice that I used two different techniques to initialize `x` and `y`.
The first example assigned `x` and `y` in the class body.
In the second example, `x` and `y` are passed to `__init__` and bound to `self`.
There is a subtle difference between the two: the first technique creates
*class* attributes while the second creates *instance* attributes.
Class attributes are attached to the class and therefore shared by all instances.
Instance attributes are unique to all instances.
In the first example, `p.x` resolved to the instance attribute while `p.y`
resolved to the class attribute.
In the second example, `p.x` and `p.y` *both* resolved to instance attributes;
there are no `x` or `y` class attributes in `Point`.

There are advantages and disadvantages to both.
If you expect most instances to use the default values, then class attributes
may be more memory-efficient.
However, if you want values to be supplied to the `__init__` method, then it
doesn't make sense to duplicate the assignment code.
Also, since class attributes are shared, if a class attribute is mutable (e.g. a
`list` or `dict`), then if *any* instance modifies it, then *all* instances will
see the modification. This may or may not be desired.

In addition to the initializer `__init__`, there is also the constructor `__new__`.
`__new__` must create *and return* the new instance (it is an error for
`__init__` to return anything other than `None`).
The returned instance's `__init__` method is then called.
Normally, `__new__` is only defined when extending immutable types, because
`__init__` must modify the instance, which is only possible for mutable types.
In most cases, just defining `__init__` is sufficient.
See the documentation for
[new](https://docs.python.org/3/reference/datamodel.html#object.__new__) and
[init](https://docs.python.org/3/reference/datamodel.html#object.__init__).

If a class defines an attribute `__slots__`, it must be a sequence of (`str`)
names which will be defined in an instance's namespace.
Instances of a class with `__slots__` will reserve only enough memory for the
names defined in `__slots__`.
This can reduce the memory usage for a class which has a lot of instances.
It can also prevent additional names from being added to an instance's namespace.
To allow an instance to have additional names bound to its namespace, include
`"__dict__"` in `__slots__`.
Class attributes cannot be used to initialize the names defined in `__slots__`.
When extending a class that defines `__slots__`, the subclass's `__slots__` must
only declare *additional* slots.
Using the same name in the subclass's `__slots__` and the base class's
`__slots__` can break the program.
See [slots](https://docs.python.org/3/reference/datamodel.html#slots).

{% highlight python %}
class Point:
    __slots__ = ['x', 'y']
    def __init__(self, x=0, y=0):
        self.x = x
        self.y = y

{% endhighlight %}

Most object-oriented languages support a notion of `public` and `private`
members.
Public members are available to all code that uses the instance, while private
members are visible only to the instance's methods.
Python does not support this distinction.
By convention, any attribute whose name starts with `_` is considered private,
but the interpreter does not enforce this.

When a name inside a class definition starts with at least two underscores and
does not end with at least two underscores (i.e. it ends with no more than one
underscore), then that name undergoes
[name mangling](https://docs.python.org/3/reference/expressions.html#atom-identifiers).
The class name with one leading underscore is prepended to the name.
This is to prevent "private" names in a subclass from overriding "private" names
in a superclass, which may break the superclass.

Subclasses are created by placing the names of superclasses in parentheses after
the class name.
Subclasses can override attributes defined in base classes.
Overridden methods can access the base class implementation using
[super](https://docs.python.org/3/library/functions.html#super).
See [guide to using super()](https://rhettinger.wordpress.com/2011/05/26/super-considered-super/).
Note that you **must** explicitly call `super().__init__()` in your `__init__`
method; this is not done automatically.

{% highlight pycon %}
>>> class Point3D(Point):
...     __slots__ = ['z']
...     def __init__(self, x=0, y=0, z=0):
...         super().__init__(x, y)
...         self.z = z
... 
>>> p2 = Point3D(3, 4, 5)
>>> p2.x
3
>>> p2.y
4
>>> p2.z
5
{% endhighlight %}

Python supports multiple inheritance, where a class can have multiple base classes.
This is commonly used to support [mix-in classes](https://en.wikipedia.org/wiki/Mixin).

{% highlight python %}
class A: pass
class B: pass
class C(A, B): pass
{% endhighlight %}

## Overloading Operators

Most operators in Python are implemented by methods with
[special names](https://docs.python.org/3/reference/datamodel.html#special-method-names).
The most obvious when running Python in interactive mode is converting an object
to its string representation. This is done using `__repr__`.

{% highlight pycon %}
>>> class Point:
...     def __init__(self, x=0, y=0):
...         self.x = x
...         self.y = y
...     def __repr__(self):
...         return "Point({0.x}, {0.y})".format(self)
... 
>>> Point()
Point(0, 0)
>>> p = Point(3, 5)
>>> p
Point(3, 5)
{% endhighlight %}

(There is also a `__str__` method. Its default is to call `__repr__`.
The difference is that the result of `__repr__` should look like a call to the
class's constructor, while the result of `__str__` should just look informative.
See
[repr](https://docs.python.org/3/reference/datamodel.html#object.__repr__)
and
[str](https://docs.python.org/3/reference/datamodel.html#object.__str__).)

The following methods implement the comparison operators:

- `__lt__`: `<`
- `__le__`: `<=`
- `__eq__`: `==`
- `__ne__`: `!=`
- `__gt__`: `>`
- `__ge__`: `>=`


These methods should return `True`, `False` or `NotImplemented`.
The latter indicates that the class does not know how to compare the arguments.
If one of these methods returns `NotImplemented`, then Python will swap the
operands and call the complementary method on the other object
(e.g. `x == y` becomes either `x.__eq__(y)` or `y.__eq__(x)`).

For immutable types, if you implement `__eq__` you should also implement
`__hash__`.
See [hash](https://docs.python.org/3/reference/datamodel.html#object.__hash__)
and my section on
[implementing hashable types]({% post_url tutorials/python/basic/2016-01-09-python-basics-1 %}#implementing-hashable-types).

To implement the arithmetic operators, see the section
[emulating numeric types](https://docs.python.org/3/reference/datamodel.html#emulating-numeric-types).

When called, the operator methods must return either the result of the operation
or `NotImplemented`.
If the method returns `NotImplemented`, then the other operand's *reflected*
operator method.
For example, to implement `x + y`, either `x`'s class must define `__add__` to
accept instances of `y`'s class, or `y`'s class must define `__radd__` to accept
instances of `x`'s class.

There are also *inline* versions of the operators.
These operators should attempt to perform the modification in-place (i.e. modify
`self`) and return the result.
The result is bound to the name to the left of the operator (e.g. `x += y`
becomes `x = x.__iadd__(y)`).
There are no reflected versions of these methods (it wouldn't make sense).

To implement a container, see
[emulating container types](https://docs.python.org/3/reference/datamodel.html#emulating-container-types).

The `len` function is implemented by the `__len__` method.
The index operator is implemented by the methods `__getitem__`, `__setitem__`
and `__delitem__`.
`for` loops expect `__getitem__` to raise an `IndexError` to detect the end of
the loop, unless you define your own iterator using `__iter__`.
`__iter__` and `__reversed__` are used to implement iterators.
They must create and return an iterator object.
A common shortcut is to implement them as generators.

The call operator is defined by `__call__`.
Arguments to the call are arguments to `__call__`.
See [the documentation](https://docs.python.org/3/reference/datamodel.html#object.__call__).

{% highlight pycon %}
>>> class Multiplier:
...     def __init__(self, x):
...         self.x = x
...     def __call__(self, y):
...         return self.x * y
... 
>>> m5 = Multiplier(5)
>>> m5(7)
35
>>> m5(100)
500
>>> m5(65536)
327680
{% endhighlight %}

## Decorators

A *decorator* is a special type of callable which accepts a callable as a
parameter and returns a callable.
Python has special syntax for decorators so that they can be applied when the
callable is defined.

{% highlight python %}
@decorator
def foo():
    pass

# is the same as
def foo():
    pass
foo = decorator(foo)
{% endhighlight %}

Decorators can be used to register a function to be called at some other time
by other code.
For example, the [atexit](https://docs.python.org/3/library/atexit.html) module
contains a `register` decorator which registers the function to be executed when
the interpreter shuts down.

{% highlight python %}
import atexit

@atexit.register
def cleanup():
    #...
{% endhighlight %}

A decorator can be any callable object.
Since classes are callable (calling them creates an instance), a class which
defines `__call__` can also be used as a decorator.

{% highlight python %}
class myDecorator(object):

    def __init__(self, f):
        print("inside myDecorator.__init__()")
        f() # Prove that function definition has completed

    def __call__(self):
        print("inside myDecorator.__call__()")

@myDecorator
def aFunction():
    print("inside aFunction()")
print("Finished decorating aFunction()")
{% endhighlight %}

Classes themselves can also be decorated.

{% highlight pycon %}
@decorator
class Foo:
    #...

# is the same as
class Foo:
    #...
Foo = decorator(Foo)
{% endhighlight %}

There is a list of decorator recipes in the
[Python Decorator Library](https://wiki.python.org/moin/PythonDecoratorLibrary/).

## Static and Class Methods

Static methods do not receive an instance as a parameter.
A class with static methods serves as a namespace for the methods.
Static methods can also serve as alternative constructors with more descriptive
names.
A static method is created with the `@staticmethod` decorator.

{% highlight python %}
class LinkedList:
    class Node:
        def __init__(self, value=None, next=None):
            self.value = value
            self.next = next

    def __init__(self):
        self._head = None

    @staticmethod
    def fromSeq(seq):
        """Create a list from any finite sequence."""
        ll = LinkedList()
        it = iter(seq)
        prev = ll._head = LinkedList.Node(next(it))
        for item in it:
            node = LinkedList.Node(item)
            prev.next = node
            prev = node
        return ll

    @staticmethod
    def fromCons(cons):
        """
        Create a list from conses.

        A cons is a list l where l[0] is the value and l[1] is the rest of the list.
        """
        head = cons[0]
        cons = cons[1]
        prev = ll._head = LinkedList.Node(head)
        while cons:
            head = cons[0]
            node = LinkedList.Node(head)
            prev.next = node
            prev = node
            cons = cons[1]
        return ll

l1 = LinkedList.fromSeq([1, 2, 3])
l2 = LinkedList.fromCons([1, [2, [3, []]]])
{% endhighlight %}

A class method receives a class as its first parameter, instead of an instance.
These can be more flexible than static methods, especially with inheritance.
Class methods are created with the `@classmethod` decorator.

The following creates a base class and registry for website parsers.
Subclasses must either define class attributes `NETLOC_PATTERN` and
`PATH_PATTERN` or override the class method `can_handle_url`.
Users can get a parser for a certain website by passing the URL to
`ParserFactory.get_parser(url)`.

{% highlight python %}
from html.parser import HTMLParser
from urllib.parse import urlparse

class ParserNotFound(Exception):
    pass

class ParserFactory(HTMLParser):
    @classmethod
    def get_parser(cls, url):
        parsed_url = urlparse(url)

        for subclass in cls.__subclasses__():
            if subclass.can_handle_url(parsed_url):
                return subclass

        raise ParserNotFound('no parser found for url "{}"'.format(url))

    NETLOC_PATTERN = re.compile('^$')
    PATH_PATTERN = re.compile('.*')

    @classmethod
    def can_handle_url(cls, parsed_url):
        return (cls.NETLOC_PATTERN.match(parsed_url.netloc) is not None
                and cls.PATH_PATTERN.match(parsed_url.path) is not None)

parserClass = ParserFactory.get_parser(url)
parser = parserClass()
{% endhighlight %}

## Properties

Properties are attributes which are accessed like fields but behave like methods.
Properties are created with the
[property](https://docs.python.org/3/library/functions.html#property) decorator.

{% highlight python %}
class C:
    def __init__(self):
        self._x = None

    @property
    def x(self):
        """I'm the 'x' property."""
        return self._x

    @x.setter
    def x(self, value):
        self._x = value

    @x.deleter
    def x(self):
        del self._x

o = C()
print(o.x)
o.x = val
del o.x
{% endhighlight %}

## Descriptors

Descriptors are the magic behind Python's methods and properties.
A [descriptor](https://docs.python.org/3/reference/datamodel.html#implementing-descriptors)
is defined by any of the methods `__get__`, `__set__`, and `__delete__`.
When a class attribute defines the `__get__` method, it is called and the result
is returned as the result of the attribute lookup.
Functions are descriptors.
When a function is an attribute of a class, its `__get__` method either returns
itself (when accessed via the class) or a *bound method* object (when accessed
via an instance).
The bound method prepends the instance to the argument list, thus the instance
becomes the function's `self` parameter.

More information about this process is described in
[From Function to Method](https://wiki.python.org/moin/FromFunctionToMethod).
A full discussion can be found in
[the Descriptor HowTo Guide](https://docs.python.org/3/howto/descriptor.html).

## Metaclasses

Just like objects are instances of classes, classes are instances of metaclasses.
When a class specifies a metaclass (or inherits from a class which specifies a
metaclass), the following occurs:

- The metaclass's `__prepare__` class method is called with arguments `name`,
  `bases`, and `**kwds`.
    - `name` is the name of the new class
    - `bases` is a `tuple` of the base classes
    - `**kwds` is any keyword arguments specified after the base classes.

  It must return a `dict` representing the class's namespace
- The class's body is executed.
- The metaclass is instantiated, with the following arguments to the constructor:
    - `name` is the name of the new class
    - `bases` is a `tuple` of the base classes
    - `namespace` is a `dict` containing the updated namespace.
    - `**kwds` is any keyword arguments specified after the base classes.


Note that most examples of metaclasses use `__new__` instead of `__init__`.
This is **not** required.
In fact, it
[can be more useful](http://www.kr41.net/2013/07-18-python_metaclasses____new___vs____init__.html)
to use `__init__`.

Since classes are instances of metaclasses, any methods defined in the metaclass
will be inherited by the class.
In particular, the metaclass can implement operator methods, allowing operators
to be used on class objects.
For example, if a metaclass defines `__call__`, then attempting to instantiate
a class which uses this metaclass will actually call the `__call__` method.

The [enum](https://hg.python.org/cpython/file/3.5/Lib/enum.py)
module in the standard library uses this in the `EnumMeta` metaclass.
The `EnumMeta` metaclass overrides several operator methods, such as `__call__`,
`__iter__`, and `__getitem__`.
`Enum` is an instance of `EnumMeta`.
All subclasses of `Enum` will inherit these operators, allowing for the syntax
shown in the [examples](https://docs.python.org/3/library/enum.html).

(See [customizing class creation](https://docs.python.org/3/reference/datamodel.html#customizing-class-creation))


