---
layout: category
title: Mathmetics
---

<ul class="arc-list">
  {% assign sorted_posts = site.categories.Mathmetics | sort: 'title' %}
  {% for post in sorted_posts %}
    <li><a href="{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
