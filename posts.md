---
layout: page
title: Posts
---

List of posts.

{% for post in site.posts %}
    * {{ post.date | date: "%B %e, %Y" }} - [{{ post.title }}]({{ site.url }}{{ post.url}})
{% endfor %}
