---
layout: page
title: Saddleback College Computer Science Society
tagline: Better software for... ellipses...
---
{% include JB/setup %}

## Recent Posts

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date: "%B %e, %Y" }}</span> : <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a>{% if post.author %} - {% if post.author.github %}<a href="https://github.com/{{post.author.github | xml_escape}}">{{ post.author.name | xml_escape }}</a>{% else %}{{ post.author.name | xml_escape }}{% endif %}{% endif %}</li>
  {% endfor %}
</ul>

![beautiful, beautiful tiger](/images/tiger.jpg)

