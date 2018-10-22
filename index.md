---
layout: page
title: 
---
{% include JB/setup %}

<ul class="posts">
  {% for post in site.posts limit: 5 %}
    <li>
		<span>{{ post.date | date_to_string }}</span> &raquo; <a style="font-size:1.5em;position:absolute;margin-left:0.5em" href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a>
		<p style="padding-top:0.5em">
			{{post.excerpt | remove: '<p>' | remove: '</p>'}}
			<a href="{{ post.url }}">  [&raquo;&nbsp;read&nbsp;more...]</a>
		</p>
	</li>
	
  {% endfor %}
  <li style="list-style: none; margin-top: 1em"><span>Older Posts</span> &raquo; <a href="/archive/">Archive</a></li>
</ul>
<!-- test -->
