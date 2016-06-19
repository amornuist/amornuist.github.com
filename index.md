---
layout: default
title: 如何搭建个人blog!
tagline: 野望
---
{% include JB/setup %}
#1.1
#2.1
#3.1
<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

