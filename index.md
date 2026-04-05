---
layout: page
title: 文章索引
description: 所有博客文章的索引目录
permalink: /
---


# 📚 文章索引

> 这里收录了我所有的博客文章，持续更新中...

---

{% assign posts = site.posts | where_exp: "post", "post.url != '/posts/'" %}

{% for post in posts %}
## [{{ post.title }}]({{ post.url }})

**📅 {{ post.date | date: "%Y-%m-%d" }}** &nbsp;|&nbsp; **🏷️ {{ post.categories | join: ", " }}**

{{ post.excerpt | strip_html | truncate: 120 }}

[阅读全文 →]({{ post.url }})

---

{% endfor %}

## 📊 统计信息

- **文章总数：** {{ posts | size }}
- **更新日期：** {{ "now" | date: "%Y-%m-%d" }}
