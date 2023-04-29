---
layout: page
title: About
description: 个人介绍
keywords: Liu Jiafan
comments: true
menu: 关于
permalink: /about/
---

欢迎👏大家来看我的博客

我是来着ECNU大三的一名学生 平时超级超级喜欢拍照和美食哈哈哈哈

如果博客中有任何的问题 欢迎大家联系我并指出哦 

## 联系

<ul>
{% for website in site.data.social %}
<li>{{website.sitename }}：<a href="{{ website.url }}" target="_blank">@{{ website.name }}</a></li>
{% endfor %}
{% if site.url contains 'mazhuang.org' %}
{% endif %}
</ul>
