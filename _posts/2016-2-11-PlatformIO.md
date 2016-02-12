---
layout: post
title: PlatformIO
author:
    name: Ethan Slattery
    email: Ethan.Slattery@yahoo.com
    github: TheSlat
date: 2016-2-11 18:55:08 -08:00
description: "Setting up an"
tags: [IDE, Arduino, Clion]
---
{% include JB/setup %}
### Motivation ###
**[Cross Posted From the AutonomousCar Blog](http://saddlebackcss.github.io/AutonomousCar/blog/PlatformIO/)**

One of the biggest struggles in working with hardware is getting code to play nice with an IDE, especially in Windows. Linux of course is much easier, but I spend half my time on a windows computer. At school we have only windows, and eclipse is the default IDE. I had been using the AVR plugin for eclipse successfully for a bit, but it has several things I dislike. Mainly that the settings for libraries and tools is linked to the project not the workspace, so you either need a blank project to copy or re-do the settings for every new project. It also seems to randomly break the entire workspace, meaning I have to re-create all settings from scratch.

This all came to a head last night as I opened a project for the first time since before break, and the eclipse AVR workspace was corrupted. I only had about 2 hours to work on my project and instead of re-creating an eclipse workspace and going through all that struggle I decided to look for an alternate solution...

### Other Solutions: ###
   * **Text editor and command line:** Great for small things, but honestly for large projects with libraries I don't know well or other people writing code it is nice to have some sort of code completion, highlighting, and parameter suggestion. The main project I am working with now is also for computer club, and I *really* want to make it accessible to entry level students, so finding a development environment that is both friendly but doesn't hold back advanced students is a plus.
   * **Arduino IDE:** almost the same as above but no command line. It's great for what it is, but it makes it really hard for the other C++ students in the club to write code the way we learned how to (breaking up into multiple files, classes, functions, etc)
   * **Eclipse:** Drawbacks mentioned above, it is the default IDE at our school but I think the drawbacks make it too much for entry level students.
   * **MakeFile:** There are makefile projects out there for arduino, AVR, etc. This is great but again not great for beginners.
 
Last semester a fellow student showed me clion, and I *really* liked it. It is unfortunately not free... but students can get a free copy. The code completion and suggestions are almost magical, it has git integration, code analysis and suggestions, and uses makefiles as a base. All the project and workspace settings play nice with git, which is nice as well since eclipse definitely lacks this ability. I started using it for my class assignments for these reasons, especially an easy break in for makefiles. The only problem is that it doesn't support non-standard toolchains easily, and it seemed like too much work to modify it.

### Enter PlatformIO
I had seen platformIO before but it advertises itself as an "open source ecosystem for IoT development", and since IOT is not somethign I am into I never looked into it. But it came up as a way to get embedded harware working in clion in some google searches so I dug deeper. It was great! I was able to get my AVR project up and running in clion within 30 minutes.

### What you need: ###
 * **[Python 2.6-2.7](https://www.python.org/)** : I use 3.5, so i had to install 2.7.11
 * **[Clion](https://www.jetbrains.com/student/)** : Free for students
 * Thats it!
 
### Installing
[Here is the official quickstart guide, if my steps don't work for you](http://docs.platformio.org/en/latest/quickstart.html)

Install platforio using pip (python package manager). Make sure this is a terminal with python 2.7 in the $path$. I didn't have to install pip on my fresh install of python
{% highlight BASH %}
pip install -U platformio
{% endhighlight %}

You can find the name of the board you are using using the [http://platformio.org/#!/boards](Embedded board explorer). I am using a Arduino Diecimila with an ATMEGA328P chip. I already had a project so I made sure it matched the folder structure (with licraries in a **src** subfolder and libraries and includes in a **lib** subfolder and then ran the following command in my project folder.
{% highlight BASH %}
platformio init --ide clion --board diecimilaatmega328
{% endhighlight %}

since I already had the suggested fodler structure it downloaded all the necessary libraries and tools for an AVR/Arduino project, created the Cmakelists.txt, and a platformio.ini and was done. I opened Clion and it recognized the folder as a project and was programming! Auto complete with all the libraries, compiling, and uploading to my board with no other settings. Nice!

![Clion & AVR working!](http://saddlebackcss.github.io/AutonomousCar/assets/img/clion_avr.PNG)

I will write more once I use it a little longer, but I am very impressed so far. I also noticed that it inclued a travis.yml file, and that travis has support for continous integration build for embedded platformIO projects. Nice! There is also [integration with other IDEs](http://docs.platformio.org/en/latest/ide.html) so I might play with that to see what else there is for the day I eventually lose my free Clion liscense, or try to find a good open source alternative out of principle.
