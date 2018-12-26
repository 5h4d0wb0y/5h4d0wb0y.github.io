---
layout: page
title: 5h4d0wb0y.github.io
subtitle: 5h4d0wb0y's information security blog focused on penetration testing, video tutorials and tools.
css: "/css/index.css"
permalink: /tutorials
use-site-title: true
bigimg: /img/logo.jpg
---

<div class="list-filters">
  <a href="/" class="list-filter">All posts</a>
  <a href="/popular" class="list-filter">Most Popular</a>
  <span class="list-filter filter-selected">Tutorials</span>
  <a href="/tags" class="list-filter">Tags</a>
</div>

<div class="posts-list">
  {% for post in site.tags.tutorial %}
  <article>
    <a class="post-preview" href="{{ post.url | prepend: site.baseurl }}">
	    <h2 class="post-title">{{ post.title }}</h2>
	
	    {% if post.subtitle %}
	    <h3 class="post-subtitle">
	      {{ post.subtitle }}
	    </h3>
	    {% endif %}
      <p class="post-meta">
        <span class="fa fa-calendar"></span> Posted on {{ post.date | date: "%B %-d, %Y" }}
        {% if post.lastupdated %}
        | <span class="fa fa-refresh"></span> Last updated {{ post.lastupdated | date: "%B %-d, %Y" }}
        {% endif %}
        {% include read_time.html content=content %}
      </p>

      <div class="post-entry">
        {{ post.content | truncatewords: 50 | strip_html | xml_escape}}
        <span href="{{ post.url | prepend: site.baseurl }}" class="post-read-more">[Read&nbsp;More]</span>
      </div>
    </a>  
   </article>
  {% endfor %}
</div>