---
layout: page
title: Portfolio
---

#Portfolio Page
Work in progress page...

{% for portfolioitem in site.portfolio %}<span class="archivemono">{{ portfolioitem.date | date_to_string }}</span> &raquo; [ {{ portfolioitem.title }} ]({{ portfolioitem.url }})  
{% endfor %}
