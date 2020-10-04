---
title: Categories
---
{% for category in site.categories %}
- [{{ category.name }}]({{ category.url }})
{% endfor %}