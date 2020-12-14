---
layout: default 
title: "Home"
permalink: /
---

# Welcome to Sergey's blog 

Place holder for description of this blog

{% for post in site.posts limit:4 %}
  <h2> [ {{ post.title }} ]({{ post.url }}) </h2>
  {{ post.date | date_to_string }}  
  {{ post.content }}
{% endfor %}

[Read more](/archive/index.html)
