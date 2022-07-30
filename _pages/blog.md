---
title: Blog
description: "All of the posts in Alireza Samar’s Website"
og-type: website
permalink: /blog
nav: custom
custom-nav: 
    - '<a href="/blog/tags" title="Tags">Tags</a>&nbsp;|&nbsp;'
    - '<a href="/blog/random" title="Random post">Random</a>'
---

{% assign display-post-type = true %}
{% for post in site.posts %}
{%- unless post.categories contains "rss-club" or post.categories contains "unlisted" -%}
{% include blog-listing.html %}
{% endunless %}
{% endfor %}