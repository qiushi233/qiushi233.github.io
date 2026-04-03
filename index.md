---
layout: page
title: 文章索引
description: 所有博客文章的索引目录
permalink: /
---

<meta http-equiv="Cache-Control" content="no-cache, no-store, must-revalidate">
<meta http-equiv="Pragma" content="no-cache">
<meta http-equiv="Expires" content="0">

<script>
/* 强制刷新按钮 */
(function() {
  function addRefreshButton() {
    var navbar = document.querySelector('.navbar-nav, .nav-links, .links');
    if (!navbar) return;
    if (document.querySelector('.refresh-btn')) return;
    
    var li = document.createElement('li');
    li.className = 'refresh-btn nav-item';
    li.innerHTML = '<a href="javascript:location.reload()" title="刷新页面" style="cursor:pointer;"><i class="fas fa-sync-alt"></i></a>';
    li.style.cssText = 'margin-left:auto;';
    navbar.appendChild(li);
  }
  
  if (document.readyState === 'loading') {
    document.addEventListener('DOMContentLoaded', function() { setTimeout(addRefreshButton, 800); });
  } else {
    setTimeout(addRefreshButton, 800);
  }
})();
</script>

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
