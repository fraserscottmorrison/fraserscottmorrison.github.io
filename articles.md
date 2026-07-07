---
layout: page
title: Articles
permalink: /articles/
---

# Articles

{% assign sorted_articles = site.articles | sort: "title" %}

{% for article in sorted_articles %}
## [{{ article.title }}]({{ article.url }})

{{ article.description }}

{{ article.excerpt }}

{% endfor %}
