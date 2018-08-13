---
layout: page
title: Vladimir Vuksan's Blog
tagline: Documenting the systems and network infrastructure madness
---
{% include JB/setup %}

<ul class="entries">
            {% for post in site.posts limit:5 %}
                <a href="{{ post.url }}">
                <h3>{{ post.title }}</h3></a>
                <p class="blogdate">{{ post.date | date: "%d %B %Y" }}</p>
                <div>{{ post.content }}</div>
                <hr><p></p>
            {% endfor %}
</ul>

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
