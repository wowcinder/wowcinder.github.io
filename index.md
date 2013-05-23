---
layout: default
title: XuehuiHe blog
---
{% include JB/setup %}

{% for post in site.posts limit:5 %}
## [{{ post.title }}]({{ post.url }}) <small>{{ post.date | date_to_long_string}}</small>
<ul class="tag_box inline">
{% assign categories_list = post.categories %}
{% include JB/categories_list %}
{% assign tags_list = post.tags %}  
{% include JB/tags_list %}
</ul>
{{ post.content | split:"<!--more-->" | first  }}
{% endfor %}
