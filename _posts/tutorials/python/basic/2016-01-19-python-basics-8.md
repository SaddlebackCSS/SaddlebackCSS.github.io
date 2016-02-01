---
layout: post
title: "Python Basics 8: Further Research"
author:
    name: Sean Raven
    github: dragon0
description: ""
tags: ["python"]
---
{% include JB/setup %}

[The Python Wiki](https://wiki.python.org/moin/)

### Books

[Automate the Boring Stuff](https://automatetheboringstuff.com/): A book about
everyday, practical Python.
Aimed at non-programmers, it covers tasks such as:

- Manipulating files (e.g. batch renaming).
- Searching the contents of files.
- Extracting text from Word and PDF files.
- Manipulating Excel spreadsheets.
- Scraping (extracting data from) websites.
- Sending e-mails and text messages.
- Image manipulation.
- GUI automation (sending keystrokes and mouse events to other programs).

[Programming Python by Mark Lutz](http://shop.oreilly.com/product/9780596158118.do):
A *very* comprehensive exploration of Python's standard library.
Covers system administration, GUIs, and web programming.

[Green Tea Press](http://greenteapress.com/) has free textbooks by Python
professor Allan Downey.

### Interacting With Other Languages

[extending and embedding](https://docs.python.org/3/extending/index.html): Shows
how to extend the CPython interpreter with modules implemented in C, and
progresses to embedding the CPython interpreter into other C programs.

[C api](https://docs.python.org/3/c-api/index.html):
Describes CPython's C API.

[Cython](http://cython.org/):
An extension of Python that compiles into C extensions for CPython.
Allows for writing C code using Python syntax.

#### Programs With Python Embedded

- [Blender](http://blender.org/): A powerful, all-in-one 3D studio.
  Used for 3D modeling, animation, physics simulations, and games.
  While the core is implemented in C++, the entire UI is written in Python.
  Users can access the embedded interpreter to write editing scripts, new
  add-ons, and implement physics and game logic.
- [LibreOffice](http://www.libreoffice.org/): Graphical office suite comparable
  to Microsoft Office.
  Supports text documents, spreadsheets, presentations, vector drawings,
  mathematical expressions, and databases.
  Can use Python to write macros to programmatically edit documents.
- [Gimp](http://www.gimp.org/):
  A raster graphics editor comparable to Photoshop.
  Plugins can be written in Python.


[Jython](http://www.jython.org/):
A Python interpreter written in Java.
Allows for seamless interaction with Java types.
Python code can import Java packages as though they were Python modules.
Java code can interact with Python via the
[Java Scripting API](https://today.java.net/pub/a/today/2006/04/11/scripting-for-java-platform.html)
in the `javax.script` package.

[IronPython](http://ironpython.net/):
A Python interpreter written in C#.
Like Jython does with Java, IronPython allows Python code to seamlessly interact
with `.NET` managed types.
`.NET` assemblies are imported just like Python modules (as long as the `dll`
is found on `sys.path`).

[numpy, scipy, and matplotlib](http://scipy.org/):
`numpy` is a C implementation for static arrays.
These arrays can be treated as mathematical vectors and matrices.
Scalar, vector, and matrix operations are implemented in C, making them very
fast, while using Python's operators for more expressive syntax.
`scipy` is a collection of scientific functions that operate on `numpy` arrays.
`matplotlib` is a plotting library based on MATLAB.
It can be used to make 2D and 3D plots of `numpy` arrays.

[pysfml](http://www.python-sfml.org/):
Python wrapper for the C++ library SFML (Simple Fast Multimedia Library).
SFML is used to make media applications and games.

### Web Scraping

Web "scraping" is extracting data from a web page.
Web pages are written in HTML.
Often people want to extract certain data from a specific part of the page.
This requires parsing the HTML.
While Python includes `html.parser`, it can be tricky to use it to implement
logic for complex pages.
Python also includes DOM and ETree XML parsers, but these require the web page
to be well-formed XML, which they often aren't.
The following are easier to use than the included modules.

[Beautiful Soup](http://www.crummy.com/software/BeautifulSoup/):
The most popular HTML parser for Python.
It is very easy to use and very tolerant of poorly-formed/invalid HTML.
`pip install beautifulsoup4`

[PyQuery](https://pythonhosted.org/pyquery/):
A Python rewrite of the popular JavaScript library `jQuery`.
Can be used with any kind of XML document.

### Server-Side Frameworks

These are libraries which support applications running on a Web server.
They allow Python to process HTTP requests and return responses to the browser.
Several tasks might be required in between: authenticating the user; accessing
a database; generating HTML; and more.
These frameworks help implement common tasks.
Those new to web programming should pick one framework and stick with it, rather
than try to learn all of them simultaneously.

Learn more about server-side frameworks at
[Full Stack Python](http://fullstackpython.com/)
(specifically
[Bottle](http://www.fullstackpython.com/bottle.html),
[Flask](http://www.fullstackpython.com/flask.html),
[Django](http://www.fullstackpython.com/django.html),
[Pyramid](http://www.fullstackpython.com/pyramid.html),
and [others](http://www.fullstackpython.com/other-web-frameworks.html)).

### Keep Your Brain Working

[Hy](http://hylang.org/) is a Lisp parser for Python.
It parses Lisp code into Python syntax to be executed by the Python interpreter.
See also my [hy quickref]({% post_url tutorials/python/2016-01-20-python-hy-quickref %}).

Python is a very powerful language that can be
[abused wonderfully](http://www.kr41.net/2013/05-19-obscene_python.html).

