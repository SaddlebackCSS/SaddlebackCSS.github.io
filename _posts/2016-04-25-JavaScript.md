---
layout: post
title: A few sites to learn programming languages
author:
    name: Sean Raven
    github: dragon0
date: 2016-04-12 19:30:00 -7:00
description: "Some websites for learning new programming languages"
tags: [programming]
---
{% include JB/setup %}

Below are my notes from the JavaScript presentation last week.

---

"use strict";

# JavaScript

JavaScript is an embedded scripting language.
JavaScript does not do anything useful on its own.
It must be embedded in a host application which provides useful functionality.

	class MyClass{ ... }
	MyClass myObj = new MyClass();
	engine.put("theObject", myObj);

The most common hosts are Web browsers.

The main implementations are:

- Google V8
- Mozilla SpiderMonkey
- Microsoft JScript


You can write your own host.

- Java Scripting API allows you to write a host in Java.
- QScript module allows you to write a host using Qt.
- You can embed an existing interpreter in your own C/C++ application.


Other noteworthy implementations:

- Node.js is a host for V8 that enables low-level access to the OS. It is getting popular to use on Web servers (see MEAN stack).
- Windows Script Host allows JScript to access ActiveX objects. This is the closest thing Windows has to Bash (including "Bash for Windows").
- Java 6 and later ship with a JavaScript interpreter (either Mozilla Rhino or Oracle Nashorn) which can be accessed through the Scripting API.


## JavaScript Basics

### Types

Everything is an Object.
Variables are just pointers to Objects.
"Primitives" are just Objects which can be created using special syntax.
There are 7 primitive types and 2 special values.

The special values are `null` and `undefined`.
Variables which are not initialized are automatically set to `undefined`.
Otherwise, there is no significant difference between `undefined` and `null`.

The 7 primitive types are Boolean, Number, String, RegExp, Object, Array, and Function.

#### [Booleans](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Boolean)

Booleans are created using the literals `true` and `false`.
They can be combined using the operators `&&` and `||`.
Booleans can be flipped using `!`.

#### Numbers

JavaScript does not have distinct integer and floating-point types.
There are also the special numbers `NaN` and `Infinity`.
`1/0` evaluates to `Infinity` and `0/0` evaluates to `NaN`.

You can convert a String to a Number using `Number.parseInt` or `Number.parseFloat`.
They return `NaN` if the String cannot be parsed.

If you need to use integers, use `Math.round`, `Math.floor`, or `Math.ceil`.
To check if a number is an integer, use `num === Math.round(num)`.

#### Strings

Strings can contain any Unicode character except a newline.

#### RegExps

#### Objects

#### Arrays

#### Functions

var myFunc = function(){};

var myAr = [function(){}];

var myObj = {
	method: function(){
		return this.x;
	},
	x: 4
};

myObj.method();

var method = myObj.method;

method.call(myObj);

## Web JavaScript

DOM (Document Object Model) enables JavaScript to access and manipulate the browser's internal representation of the web page.

AJAX (Asynchronous JavaScript And XML) enables JavaScript to send HTTP requests to the server and process the responses.

### Libraries

#### General

[jQuery](https://jquery.com/) is an abstraction on top of DOM and AJAX which ensures cross-browser compatibility and offers convenient higher-level operations.

[Bootstrap](http://getbootstrap.com/) is a framework for creating responsive mobile-first applications.
It enables a web page to automatically reorganize itself based on the size of the user's screen.

[Underscore.js](http://underscorejs.org/) is a library for high-level array processing.

[Backbone.js](http://backbonejs.org/) is a lightweight MVC framework.
It offers better structure for your client-side code than jQuery offers on its own.
It is smaller and easier to learn than AngularJS.

[AngularJS](https://angularjs.org/) is the most popular client-side MVC framework.
Almost all Web Developer job postings require it.

[RequireJS](http://requirejs.org/) is a JavaScript file and module loader.
It can improve download performance by downloading scripts asynchronously.
It also ensures that scripts are loaded in the correct order,
which can be difficult otherwise,
since the order in which the script tags should be placed is not always obvious.

#### Games

[Phaser.js](http://phaser.io/) is the most-starred HTML5 2D game library on GitHub.
It is flexible and easy to learn, and there are many resources available for it.

[Three.js](http://threejs.org/), while not technically a game library, is often used for games.
Three is a 3D library built on top of WebGL.

[Babylon.js](http://www.babylonjs.com/) is a framework for building 3D games.

### Tools

Node.js includes `npm`, the "Node.js Package Manager".
There are many Web-oriented development tools available for download.
You can use `npm init` to create a `package.json` file in your project directory,
which lists the project's build-time and run-time dependencies.
You or other developers on your team can then use `npm install` to install the dependencies.

Grunt and Gulp are two build systems written for Node.js.
At a minimum, they can be used to create "minimized" versions of your scripts.
These remove all unnecessary whitespace and rename local variables to have single-letter names, improving download time.
They can also be used to copy files or run other tools (such as Cordova or the Closure Compiler).

[Apache Cordova](https://cordova.apache.org/) is used to create cross-platform apps using HTML5 technologies.
You write your app in HTML5, CSS, and JavaScript,
and Cordova generates a custom browser for your app.
The custom browser exposes device APIs that the default browser does not (for security reasons).
Ionic provides Cordova with a set of components that let your app look like a native app.

Google's [Closure Compiler](https://developers.google.com/closure/compiler/)
is used to optimize your JavaScript and check for common pitfalls.
It can also be invoked during Grunt or Gulp builds.

[GWT](http://www.gwtproject.org/) (the Google Web Toolkit) compiles Java source code to JavaScript.

[Emscripten](http://kripken.github.io/emscripten-site/) is like GWT, but for C/C++.

## Other Full-Stack Frameworks

- Java
  - J2EE
  - Spring
- Python
  - Django
  - Pyramid
  - Bottle
- Ruby on Rails
- .NET MVC

## See Also

- The [Mozilla Developer Network](https://developer.mozilla.org/en-US/) has the most comprehensive reference documentation for JavaScript and other standard Web technologies.
- [W3 Schools](http://www.w3schools.com/) contains many simple tutorials and reference material. It's useful for quick lookups, but MDN is more comprehensive and authoritative.
- [Eloquent JavaScript](http://eloquentjavascript.net/) is a good introductory book on JavaScript.
