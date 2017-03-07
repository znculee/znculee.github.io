---
layout: category-root
title: Blogs
---

<ul>
  {% assign sorted_categories = site.categories | sort %}
  {% for category in sorted_categories %}
    <li><a href="{{ site.baseurl }}/blogs/{{ category | first | downcase }}">{{ category | first | replace:'_',' ' }}{{ " (" }}{{ category | last | size }}{{ ")" }}</a></li>
  {% endfor %}
</ul>
