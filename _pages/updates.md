---
title: Now Updates
description: "Updates about what I’ve been up to" 
permalink: /blog/now
---

{% for post in site.categories.now %}
{% include blog-listing.html %}
{% endfor %}