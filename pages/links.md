---
layout: page
title: Links
description: 开源项目展示
menu: 链接
permalink: /links/
---

> 一些开源项目展示

<ul>
{% for link in site.data.links %}
  {% if link.src == 'life' %}
  <li><a href="{{ link.url }}" target="_blank">{{ link.name}}</a></li>
  {% endif %}
{% endfor %}
</ul>
