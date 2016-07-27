---
layout: main
title: Journal
---
{% for post in site.posts %} * {{ post.date | date_to_string }} &rarr; [ {{ post.title }} ]({{ post.url }})
{% endfor %}
