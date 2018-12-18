---
layout: default
title: All Posts
permalink: /posts
image:
description: All posts
---

<div class="text-center">
	<h1>Post Archive</h1>
	<br/>
</div>
<div class="posts">
	<style type="text/css">
		<!--
		.tab { margin-left: 40px; }
		-->
	</style>
	{% for post in site.posts %}
		<article class="post-preview" style="margin-top: 0;">
	    <a href="{{ post.url }}" class="post-title">
	    	<h3>{{ post.title }}</h3>
	    	<h4 class="post-subtitle">{{ post.subtitle }}</h4>
	    </a>
		<p class="tab"><span class="post-meta">
		Posted on {{ post.date | date: "%B %e, %Y" }}
		{% if post.lastupdated %}
        | Last updated {{ post.lastupdated | date: "%B %-d, %Y" }}
	    {% endif %}
		{% include read_time.html content=post.content %}</span> 
		<span class="post-entry">{{ post.content | truncatewords: 100 | strip_html | xml_escape}}</span><a 	href="{{ post.url }}"><b> [more]</b></a></p>
	    </article>
	{% endfor %}
</div>