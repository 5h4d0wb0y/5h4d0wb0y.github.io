---
layout: page
title: Walkthroughs
subtitle: This section contains all walkthroughs
permalink: /walkthroughs
#bigimg: /img/logo.jpg
description: 
---

<div class="well article">
{% assign sortedcategories = site.categories | sort %}
{% for category in sortedcategories %}
    <h2>{{ category[0] | capitalize }}</h2>
    <ul>
        {% assign pages_list = category[1] %}
        {% for node in pages_list %}
            {% if node.title != null %}
            {% if group == null or group == node.group %}
                <li><span class="post-date">
                    {{ node.date | date: "%B %e, %Y" }} </span><a href="{{ site.baseurl}}{{ node.url }}">{{ node.title }}</a></li>
            {% endif %}
            {% endif %}
        {% endfor %}
        {% assign pages_list = nil %}
    </ul>
{% endfor %}
</div>











