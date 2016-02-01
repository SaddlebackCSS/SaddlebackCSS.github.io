---
layout: post
title: Publishing an Article
author:
    name: Sean Raven
    github: dragon0
description: ""
category: lessons
tags: ["jekyll"]
---
{% include JB/setup %}

[mastering-markdown]: https://guides.github.com/features/mastering-markdown/
[markdown]: http://daringfireball.net/projects/markdown/
[jekyll]: http://jekyllrb.com/
[jekyll bootstrap]: http://jekyllbootstrap.com/

This article will describe how to author pages and blog posts.
Posts are authored in [Markdown][], see references [here][mastering-markdown].
The site is compiled using [Jekyll][], with some help from [Jekyll Bootstrap][].

Blog posts are created in the `_posts` directory.
Subdirectories are used to create categories.
The filename must be `DATE-TITLE.md` (eg `_posts/admin/2015-12-29-publishing-an-article.md`).
The file **must** begin with a YAML Front Matter block, even if it is empty.
YAFM blocks are delimited by lines containing three dashes.

    ---
    ---

The file should have the following entries in the YAFM block:


    ---
    layout: post
    title: My Post
    author:
        name: Your Name
        email: you@yoursite.example.com
        github: username
    date: 2015-12-29 13:59:00 -08:00
    description: ""
    tags: []
    ---
    {%raw%}{% include JB/setup %}{%endraw%}

- `layout` tells Jekyll which layout (in the `_layouts` directory) to use for
this file. It must be either `post` or `page`.
- `title` is used as the page title.
- `author` is for info about the post author. It can have nested entries `name`, `email`, and `github`.
    - `name` is the Author's name (required, or don't create the `author` block).
    - `email` is the Author's email address (optional, appears in newsfeeds).
    - `github` is the Author's GitHub user name (optional, creates a link to your profile).
- `date` is the date, time, and timezone of publication. Jekyll uses this to sort the posts.
- `description` is the page description which appears on the newsfeed and other pages.
- `tags` is an array of tags for the post


If you have installed Jekyll locally, you can create a new post using the `rake` command:

    $ rake post title="Hello World"

This will create a file with the correctly formatted filename and YAFM block.
Note that you must still add the `author` block.

You can also create pages using rake:

    $ rake page name="about.md"

A page is an HTML or Markdown file (with YAFM block) that does not represent
an "article".
Otherwise, a page is identical to a post except for the `layout: post` 
and lack of any `category` or `tags` entries in the YAFM block.

For more info, read the [Jekyll Quick Start](http://jekyllbootstrap.com/usage/jekyll-quick-start.html)


