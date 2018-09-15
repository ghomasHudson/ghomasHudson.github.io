---
layout: post
title: 'Thomas Hudson'
bannerImg: /assets/img/techPattern.jpg
---

I'm a programmer/data scientist working in the intersection of Security and Natural Language Processing. I'm currently studying for a PhD in Computer Science at Newcastle University

Check out my [Blog](/blog) or [CV](/cv).


---
#### Recent Articles
{% for post in site.posts limit:3 %}
[{{ post.title }}]({{ post.url }}) - {{ post.content | strip_html | truncatewords: 20 }}
{% endfor %}
