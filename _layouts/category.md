---
layout: default
---
<h3>category: {{ page.name }}</h3>

{% assign posts = site.posts | where_exp: "post", "post.categories contains page.name" %}

{% include posts.md %}