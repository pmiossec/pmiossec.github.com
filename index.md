---
layout: page
title: My Last posts!
tagline: Supporting tagline
---
{% include JB/setup %}

<div class="posts">
  {% for post in site.posts limit:10 %}
	<h2><a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></h2>
	
	{{ post.content | truncatewords: 50 | textilize }}
	
	<aside>
		<time pubdate datetime="{{ post.date | date: "%Y-%m-%d" }}">{{ post.date | date: "%d/%m/%Y" }}</time>, {{ post.categories | category_links }} | 
		<a rel="nofollow" href="{{ BASE_PATH }}{{ post.url }}">Read more &raquo;</a>
	</aside>
  {% endfor %}
</div>
