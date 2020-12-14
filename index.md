---
layout: default 
title: "Home"
permalink: /
---

# Welcome to Sergey's blog 

Place holder for description of this blog

{% for post in site.posts limit:4 %}
# [ {{ post.title }} ]({{ post.url }}) 
{{ post.date | date_to_string }}  
{{ post.excerpt }}
{% endfor %}

[Read more](/archive/index.html)
