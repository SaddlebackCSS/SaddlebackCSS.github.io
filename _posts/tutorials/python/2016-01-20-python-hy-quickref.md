---
layout: post
title: "Python to Hy Quickref"
author:
    name: Sean Raven
    github: dragon0
description: ""
category: lessons
tags: ["python"]
---
{% include JB/setup %}

A quick translation table from Python to [Hy](http://hylang.org/).
Hy is a Lisp parser for Python.
It parses Lisp code into Python syntax to be executed by the Python interpreter.
See also
[The Nature of Lisp](http://www.defmacro.org/ramblings/lisp.html),
[Casting SPELs in Lisp](http://www.lisperati.com/casting.html),
[Practical Common Lisp](http://www.gigamonkeys.com/book/).

- Earmuffs become uppercase (`*foo*` -> `FOO`)
- Dashes become underscores (`--name--` -> `__name__`)


Categories:

- [Control Flow](#control-flow)
- [Built-In Sequences](#built-in-sequences)
- [Importing](#importing)
- [Functions](#functions)
- [Macros](#macros)

[assignment](http://docs.hylang.org/en/latest/language/api.html#def-setv):

<div class="row">
<div class="col-sm-6 col xs-12">
{% highlight python %}
y = m * x + b
{% endhighlight %}
</div>
<div class="col-sm-6 col xs-12">
{% highlight hylang %}
(setv y (+ (* m x) b))
{% endhighlight %}
</div>
</div>

[methods](http://docs.hylang.org/en/latest/tutorial.html#hy-is-a-lisp-flavored-python):

<div class="row">
<div class="col-sm-6 col xs-12">
{% highlight python %}
"  foo  ".strip()
"Hi {}, I'm {}".format("Bill", "Bob")
{% endhighlight %}
</div>
<div class="col-sm-6 col xs-12">
{% highlight hylang %}
(.strip "  foo  ")
(.format "Hi {}, I'm {}" "Bill" "Bob")
{% endhighlight %}
</div>
</div>

[multi-statement blocks](http://docs.hylang.org/en/latest/language/api.html#do):

<div class="row">
<div class="col-sm-6 col xs-12">
{% highlight python %}
if True:
    print ("Side effects rock!")
    print ("Yeah, really!")
{% endhighlight %}
</div>
<div class="col-sm-6 col xs-12">
{% highlight hylang %}
(if true
  (do (print "Side effects rock!")
      (print "Yeah, really!")))
{% endhighlight %}
</div>
</div>

[classes](http://docs.hylang.org/en/latest/language/api.html#defclass):

<div class="row">
<div class="col-sm-6 col xs-12">
{% highlight python %}
class Cat():
    age = None
    colour = "white"

    def speak(self): print("Meow")

spot = Cat()
spot.colour = "Black"
spot.speak()
{% endhighlight %}
</div>
<div class="col-sm-6 col xs-12">
{% highlight hylang %}
(defclass Cat []
  [age None
   colour "white"]

  (defn speak [self] (print "Meow")))

(def spot (Cat))
(setv spot.colour "Black")
(.speak spot)
{% endhighlight %}
</div>
</div>

[let](http://docs.hylang.org/en/latest/language/api.html#let) creates temporary
variables which only exist in its body:

<div class="row">
<div class="col-sm-6 col xs-12">
{% highlight python %}
# no Python equivalent
{% endhighlight %}
</div>
<div class="col-sm-6 col xs-12">
{% highlight hylang %}
(let [x 5
      y (+ x 1)] (print x y))
{% endhighlight %}
</div>
</div>

### Control Flow

[if](http://docs.hylang.org/en/latest/language/api.html#if-if-if-not)
(lispers prefer [cond](http://docs.hylang.org/en/latest/language/api.html#cond)):

<div class="row">
<div class="col-sm-6 col xs-12">
{% highlight python %}
if n < 0.0: return "negative"
elif n == 0.0: return "zero"
elif n > 0.0: return "positive"
else: return "not a number"
{% endhighlight %}
</div>
<div class="col-sm-6 col xs-12">
{% highlight hylang %}
(if (< n 0.0) "negative"
    (= n 0.0) "zero"
    (> n 0.0) "positive"
    "not a number")
{% endhighlight %}
</div>
</div>

[when](http://docs.hylang.org/en/latest/language/api.html#when) and
[unless](http://docs.hylang.org/en/latest/language/api.html#unless) are
simple one-branch conditionals.

[while](http://docs.hylang.org/en/latest/language/api.html#while):

<div class="row">
<div class="col-sm-6 col xs-12">
{% highlight python %}
while True: print("Hello World")
{% endhighlight %}
</div>
<div class="col-sm-6 col xs-12">
{% highlight hylang %}
(while True (print "Hello World"))
{% endhighlight %}
</div>
</div>

[for](http://docs.hylang.org/en/latest/language/api.html#for):

<div class="row">
<div class="col-sm-6 col xs-12">
{% highlight python %}
for element in collection:
    process(element)
else:
    complete(collection)

for key, value in mydict.items():
    process(key, value)
{% endhighlight %}
</div>
<div class="col-sm-6 col xs-12">
{% highlight hylang %}
(for [element collection]
    (process element)
    (else (complete collection)))

(for [(, key value) (.items mydict)]
    (process key value))
{% endhighlight %}
</div>
</div>

[try](http://docs.hylang.org/en/latest/language/api.html#try):

<div class="row">
<div class="col-sm-6 col xs-12">
{% highlight python %}
try:
    error_prone_function()
except ZeroDivisionError as e:
    print("Division by zero")
else:
    print("no errors")
finally:
    print("all done")
{% endhighlight %}
</div>
<div class="col-sm-6 col xs-12">
{% highlight hylang %}
(try
    (error-prone-function)
    (except [e ZeroDivisionError] (print "Division by zero"))
    (else (print "no errors"))
    (finally (print "all done")))
{% endhighlight %}
</div>
</div>

[with](http://docs.hylang.org/en/latest/language/api.html#with):

<div class="row">
<div class="col-sm-6 col xs-12">
{% highlight python %}
with open("NEWS") as f:
    print(f.read())
{% endhighlight %}
</div>
<div class="col-sm-6 col xs-12">
{% highlight hylang %}
(with [f (open "NEWS")]
    (print (.read f)))
{% endhighlight %}
</div>
</div>

### Built-In Sequences

[built-in sequences](http://docs.hylang.org/en/latest/tutorial.html#hy-is-a-lisp-flavored-python):

<div class="row">
<div class="col-sm-6 col xs-12">
{% highlight python %}
[1, 2, 3]
(1, 2, 3)
{"cat": "meow", "dog": "bark"}
{% endhighlight %}
</div>
<div class="col-sm-6 col xs-12">
{% highlight hylang %}
[1, 2, 3]
(, 1 2 3)
{"cat" "meow" "dog" "bark"}
{% endhighlight %}
</div>
</div>

[list](http://docs.hylang.org/en/latest/language/api.html#list-comp),
[set](http://docs.hylang.org/en/latest/language/api.html#set-comp),
[dict](http://docs.hylang.org/en/latest/language/api.html#dict-comp)
comprehensions and
[genexprs](http://docs.hylang.org/en/latest/language/api.html#genexpr):

<div class="row">
<div class="col-sm-6 col xs-12">
{% highlight python %}
[x*x for x in range 10 if x%2==0]
{x*x for x in range 10 if x%2==0}
{x:x*x for x in range 10 if x%2==0}
(x*x for x in range 10 if x%2==0)
{% endhighlight %}
</div>
<div class="col-sm-6 col xs-12">
{% highlight hylang %}
(list-comp   (* x x) [x (range 10)] (= (% x 2) 0))
(set-comp    (* x x) [x (range 10)] (= (% x 2) 0))
(dict-comp x (* x x) [x (range 10)] (= (% x 2) 0))
(genexpr     (* x x) [x (range 10)] (= (% x 2) 0))
; NOTE only one =
{% endhighlight %}
</div>
</div>

[indexing](http://docs.hylang.org/en/latest/language/api.html#get):

<div class="row">
<div class="col-sm-6 col xs-12">
{% highlight python %}
mylist[index]
mydict[key]
{% endhighlight %}
</div>
<div class="col-sm-6 col xs-12">
{% highlight hylang %}
(get mylist index)
(get mydict key)
{% endhighlight %}
</div>
</div>

[item assignment](http://docs.hylang.org/en/latest/language/api.html#assoc):

<div class="row">
<div class="col-sm-6 col xs-12">
{% highlight python %}
mylist[index] = value
mydict[key]   = value
{% endhighlight %}
</div>
<div class="col-sm-6 col xs-12">
{% highlight hylang %}
(assoc mylist index value)
(assoc mydict key   value)
{% endhighlight %}
</div>
</div>

[variable/index/item deletion](http://docs.hylang.org/en/latest/language/api.html#del):

<div class="row">
<div class="col-sm-6 col xs-12">
{% highlight python %}
del dic["foo"]
{% endhighlight %}
</div>
<div class="col-sm-6 col xs-12">
{% highlight hylang %}
(del (get dic "foo"))
{% endhighlight %}
</div>
</div>

[slicing](http://docs.hylang.org/en/latest/language/api.html#cut):

<div class="row">
<div class="col-sm-6 col xs-12">
{% highlight python %}
mylist[:]
mylist[3:]
mylist[3:-2]
mylist[3:-2:2]
{% endhighlight %}
</div>
<div class="col-sm-6 col xs-12">
{% highlight hylang %}
(cut mylist)
(cut mylist 3)
(cut mylist 3 -2)
(cut mylist 3 -2 2)
{% endhighlight %}
</div>
</div>

### Importing

[import](http://docs.hylang.org/en/latest/language/api.html#import):

<div class="row">
<div class="col-sm-6 col xs-12">
{% highlight python %}
import sys, os.path
from os.path import exists, isdir, isfile
import sys as systest
from sys import *
{% endhighlight %}
</div>
<div class="col-sm-6 col xs-12">
{% highlight hylang %}
(import sys os.path)
(import [os.path [exists isdir isfile]])
(import [sys :as systest])
(import [sys [*]])

(import [tests.resources [kwtest function-with-a-dash]]
        [os.path [exists isdir isfile]]
        [sys :as systest])

{% endhighlight %}
</div>
</div>

To import Hy modules in Python, you must first `import hy`.

To use macros defined in another module, you must use
[require](http://docs.hylang.org/en/latest/language/api.html#require).

### Functions

[defining functions](http://docs.hylang.org/en/latest/language/api.html#defn):

<div class="row">
<div class="col-sm-6 col xs-12">
{% highlight python %}
def foo(x):
    "docstring"
    return x
{% endhighlight %}
</div>
<div class="col-sm-6 col xs-12">
{% highlight hylang %}
(defn foo [x]
    "docstring"
    x)
{% endhighlight %}
</div>
</div>

[unpacking argument lists](http://docs.hylang.org/en/latest/language/api.html#apply):

<div class="row">
<div class="col-sm-6 col xs-12">
{% highlight python %}
foo(*args, **kwargs)
foo(**kwargs)
foo(arg0, arg1, arg2, **kwargs)
{% endhighlight %}
</div>
<div class="col-sm-6 col xs-12">
{% highlight hylang %}
(apply foo args kwargs)
(apply foo []   kwargs)
(apply foo [arg0, arg1, arg2] kwargs)
{% endhighlight %}
</div>
</div>

[lambdas](http://docs.hylang.org/en/latest/language/api.html#lambda-fn):

<div class="row">
<div class="col-sm-6 col xs-12">
{% highlight python %}
map(lambda x: x * x, mylist)
{% endhighlight %}
</div>
<div class="col-sm-6 col xs-12">
{% highlight hylang %}
(map (fn [x] (* x x)) mylist)
{% endhighlight %}
</div>
</div>

[decorators](http://docs.hylang.org/en/latest/language/api.html#with-decorator):

<div class="row">
<div class="col-sm-6 col xs-12">
{% highlight python %}
@decorator_fun
def some_function(): ...

@decorator1
@decorator2
def some_function(): ...

@decorator(arg)
def some_function(): ...

@decorator1
@decorator2
def some_function(): ...
{% endhighlight %}
</div>
<div class="col-sm-6 col xs-12">
{% highlight hylang %}
(with-decorator decorator-fun
   (defn some-function [] ...)


(with-decorator decorator1 decorator2 ...
   (defn some-function [] ...)

(with-decorator (decorator arg) ..
   (defn some-function [] ...)


#@(decorator1 decorator2
    (defn some-function [] ...))
{% endhighlight %}
</div>
</div>

### Macros

[defmacro](http://docs.hylang.org/en/latest/language/api.html#defmacro)
[quasiquote](http://docs.hylang.org/en/latest/language/api.html#quasiquote)
[unquote](http://docs.hylang.org/en/latest/language/api.html#unquote)
[unquote-splice](http://docs.hylang.org/en/latest/language/api.html#unquote-splice)

{% highlight hylang %}
(defmacro infix [code]
  `(~(get code 1) ~(get code 0) ~(get code 2)))

(infix (1 + 1))
{% endhighlight %}

