---
layout: post
title: "How Bash's Alert Alias Works"
author:
    name: Sean Raven
    github: dragon0
description: ""
tags: ["alias"]
---
{% include JB/setup %}

I was asked yesterday about an alias in Debian systems.
Here it is:

{% highlight sh %}
alias alert='notify-send --urgency=low -i "$([ $? = 0 ] && echo terminal || echo error)" "$(history|tail -n1|sed -e '\''s/^\s*[0-9]\+\s*//;s/[;&|]\s*alert$//'\'')"'
{% endhighlight %}

Let's break this down.

First off, `alert` will expand to run `notify-send`.
An obvious place to start, then, is `man notify-send`.

The manual states that the required argument is the summary, and that the
`-i` option names an icon to display.

So, the first mystery string names an icon:

{% highlight sh %}
"$([ $? = 0 ] && echo terminal || echo error)"
{% endhighlight %}

And the second is the summary:

{% highlight sh %}
"$(history|tail -n1|sed -e 's/^\s*[0-9]\+\s*//;s/[;&|]\s*alert$//')"
{% endhighlight %}

(note that the original argument to sed is delimited by `'\''`, because
the command is already inside a quoted string for the alias)

Let's focus on the icon.

In Bash, `&&` and `||` work similarly to C's boolean operators.
`&&` means "execute the first command, **and**, if it completes successfully, execute the second command".
`||` means "execute the first command, **or**, if it fails, execute the second command".
Whether a program completes successfully or not depends on its exit status,
aka the return value from `int main()`.
If `main` returns `0`, then the command is successful.
Otherwise, it failed.
(This is an inversion of C where `0` means `false` and nonzero means `true`.
This is so programs can return different status codes for different errors.)

The commands `true` and `false` can be used to demo these operators.

{% highlight sh %}
$ true && echo hi
hi
$ false && echo hi
$ true || echo hi
$ false || echo hi
hi
{% endhighlight %}

When these operators are chained, `&&` has higher precedence than `||`.
Let's see how those `echo` commands execute:

{% highlight sh %}
$ true && echo terminal || echo error
terminal
$ false && echo terminal || echo error
error
{% endhighlight %}

So, that just leaves this chunk of punctuation: `[ $? = 0 ]`

The first unobvious thing to know is that the square bracket is actually an
alternative name for the `test` command.
You can read about it using `man test`.

The `test` command is given the arguments `$? = 0`.
The `= 0` part should be self-explanatory: it's comparing something to zero.
Specifically, it's comparing `$?` to zero.
Anything that starts with a `$` is a shell variable.
The `$?` variable contains the exit status of the last command.
Remember `true` and `false`?

{% highlight sh %}
$ true
$ echo $?
0
$ false
$ echo $?
1
{% endhighlight %}

Other magical punctuation variables can be found in `man bash` in the section
"Special Parameters" (use `/` to search).

So, the expression checks the exit status, then prints either "terminal" or
"error".

{% highlight sh %}
$ true
$ [ $? = 0 ] && echo terminal || echo error
terminal
$ false
$ [ $? = 0 ] && echo terminal || echo error
error
{% endhighlight %}

This expression is wrapped in `$()`.
This is called a "Command Substitution" (again, search for this in `man bash`).
Basically, the command in parentheses is executed, and its `stdout` is spliced
into the command line.
This particular command prints either "terminal" or "error"; one of those
strings will then become the value for `notify-send`'s `-i` option.

---

Now, what about the "summary" argument?

{% highlight sh %}
"$(history|tail -n1|sed -e 's/^\s*[0-9]\+\s*//;s/[;&|]\s*alert$//')"
{% endhighlight %}

The `|` is a pipeline (again, search in `man bash`. Also, `<esc>-u` will clear
the search highlighting).
The first command is executed, its `stdout` is sent to the second command's
`stdin`, *that* command's `stdout` is sent to the third command's `stdin`, etc.
The commands in this case are `history`, `tail`, and `sed`.

The `history` command displays all of the commands you have entered so far (up
to a configurable limit).

The `tail` command prints the last few lines of its input.
The `-n1` option means to print the last `1` lines (i.e. *the* last line).

This line is then sent to `sed`, the stream editor.
`sed` is given a string of editing instructions on its command line.
It applies these instructions to its `stdin` and prints the results on `stdout`.
The most-used editing instruction is `s`, which is short for "substitute".
It performs a regular expression substitution on each line of input.

Like many languages, `sed`'s editing instructions are delimited by `;`.
This command actually contains *two* `s` instructions.

{% highlight sh %}
's/^\s*[0-9]\+\s*//'
's/[;&|]\s*alert$//'
{% endhighlight %}

The syntax for `s` is `s/pattern/replacement/`.
You can work out for yourself what the patterns are doing, but notice that
the replacement sections are empty, effectively deleting whatever text is
matched by the patterns. (hints: `man 7 regex`, look at the output of `history` compared to the message in `notify-send`)

---

Further research on Bash scripting:

- [Bash Programming HOWTO](http://tldp.org/HOWTO/Bash-Prog-Intro-HOWTO.html)
- [Bash Guide for Beginners](http://tldp.org/LDP/Bash-Beginners-Guide/html/index.html)
- [Advanced Bash-Scripting Guide](http://tldp.org/LDP/abs/html/index.html)

