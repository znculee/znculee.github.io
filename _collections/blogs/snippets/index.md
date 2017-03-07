---
layout: category
title: Snippets
---

<ul class="arc-list">
  {% assign sorted_posts = site.categories.Snippets | sort: 'title' %}
  {% for post in sorted_posts %}
    <li><a href="{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
