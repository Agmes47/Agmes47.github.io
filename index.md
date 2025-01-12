---
layout: home
title: My Blog
description: Welcome to my blog! Here, I share my thoughts, tutorials, and experiences.
---

# Welcome to My Blog!

Hi, I'm Agmes, and this is my blog. I write about everything. Feel free to explore my posts below!

---

## Recent Posts

{% for post in site.posts limit:5 %}
- [{{ post.title }}]({{ post.url }}) - {{ post.date | date: "%B %-d, %Y" }}
{% endfor %}

[View all posts](/posts)

---

## Connect with Me

- [GitHub](https://github.com/Agmes47)

---

© 2025 Agmes47. All rights reserved.
