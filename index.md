---
layout: page
title:
---
{% include JB/setup %}

<ul class="posts">
  {% for post in site.posts limit: 15 %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
  <li style="list-style: none; margin-top: 1em"><span>Older Posts</span> &raquo; <a href="/archive/">Archive</a></li>
</ul>
