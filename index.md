---
layout: home
---

# Engineering Notes

수질 모니터링 시스템, ROS2 개발 경험을 공유합니다.

## Recent Posts

{% for post in site.posts limit:5 %}
### [{{ post.title }}]({{ post.url | relative_url }})
*{{ post.date | date: "%Y-%m-%d" }}*

{{ post.excerpt }}

---
{% endfor %}

[View all posts →](/archive)
