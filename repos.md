---
layout: page
title : Repos
header : SCSS Repositories
group: navigation
---
{% include JB/setup %}

{%for repo in site.github.public_repositories %}
- [{{ repo.name }}]({{ repo.html_url }}): {{ repo.description }}
{% if repo.homepage != null and repo.homepage != "" %}([homepage]({{repo.homepage}})){%endif%}
{% endfor %}

