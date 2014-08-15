---
layout: page
title: Radio Free Coyote
tagline: easy (and difficult) listening for all free tricksers... 
---
{% include JB/setup %}

Just playing around with Jekyll and github blogs

Here, have a gorilla:

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>



