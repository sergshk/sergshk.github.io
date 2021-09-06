---
layout: default 
title: "Home"
permalink: /
---

## Thoughts of technology entrepreneur.

*Welcome to my blog. I write about technology, business, regulations and everything in between, mostly focusing on how various aspects applicable to software development and cybersecurity.*

{% for post in site.posts limit:4 %}
# [ {{ post.title }} ]({{ post.url }}) 
*{{ post.date | date_to_string }}*
{{ post.excerpt }}
[ Read more ... ]({{ post.url }}) 
{% endfor %}

[All Posts](/archive/index.html)
