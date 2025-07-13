---
layout: page
title: All Posts
permalink: /archive/
---

{% for post in site.posts %}
  * {{ post.date | date: "%Y-%m-%d" }} - [{{ post.title }}]({{ post.url | relative_url }})
{% endfor %}
