---
layout: page
title: Featured
---

Featured blog posts will be displayed here.

{% for post in site.posts %}
    <h3><a href="{{ post.url }}">{{ post.title }}</a></h3>
    <p><small><strong>{{ post.date | date: "%B %e, %Y" }}</strong> .
    <a href="{{ site.url }}{{ post.url}}"></a></small></p>			
{% endfor %}
