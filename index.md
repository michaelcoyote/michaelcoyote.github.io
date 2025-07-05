---
layout: page
title: radio free coyote
---
{% include JB/setup %}

#### ...here, have a gorilla:

<ul class="posts">
  {% for post in site.posts %}
    <li>
      <span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a>
      {% if post.description != empty %}
      <ul class="descriptions">
        <li>{{post.description}}</li>
      </ul>
      {% endif %}
    </li>
  {% endfor %}
</ul>



