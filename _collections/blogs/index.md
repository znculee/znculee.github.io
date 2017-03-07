---
layout: page
title: Blogs
---

<ul class="index-list">
  {% for post in site.posts %}
    <li class="index-item">
      <a class="index-link" href="{{ post.url }}">
        <span class="index-title">{{ post.title }}</span>
        <span class="index-meta">{{ post.date | date: "%b %-d, %Y" }}</span>
      </a>
    </li>
  {% endfor %}
</ul>
