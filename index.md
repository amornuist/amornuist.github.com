---
layout: default
title: MYblog!
tagline: 野望
---
{% include JB/setup %}

<ul class="tag_box inline">
  {% assign tags_list = site.tags %}  
  {% include JB/tags_list %}
</ul>

{% for tag in site.tags %} 
  <h2 id="{{ tag[0] }}-ref">{{ tag[0] }}</h2>
  <ul class="posts">
    {% assign pages_list = tag[1] %}  
    {% include JB/pages_list %}
  </ul>
{% endfor %}

<!--<ul class="posts">
  {% for post in site.posts %}
    <li> <span>{{post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
	<li>{{ post.excerpt }}</li>
  {% endfor %}
</ul> -->

