---
layout: default
---
<h3>{{ page.name }}</h3>

{% assign posts = site.posts | where_exp: "post", "post.categories contains page.name" %}

<ul>
{% for post in posts %}
    <li>{{ post.title }}</li>
{% endfor %}
</ul>