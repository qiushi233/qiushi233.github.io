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
(function() {
  // 主题切换按钮
  function createThemeToggle() {
    if (document.querySelector('.custom-theme-toggle')) return;
    
    var div = document.createElement('div');
    div.className = 'custom-theme-toggle';
    div.innerHTML = `
    <style>
      .custom-theme-toggle {
        position: fixed;
        bottom: 80px;
        left: 20px;
        z-index: 9999;
      }
      .custom-theme-toggle button {
        width: 50px;
        height: 50px;
        border-radius: 50%;
        border: none;
        background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
        color: white;
        font-size: 20px;
        cursor: pointer;
        box-shadow: 0 4px 15px rgba(102, 126, 234, 0.4);
        transition: all 0.3s ease;
        display: flex;
        align-items: center;
        justify-content: center;
      }
      .custom-theme-toggle button:hover {
        transform: scale(1.1);
        box-shadow: 0 6px 20px rgba(102, 126, 234, 0.6);
      }
      .custom-theme-toggle button:active {
        transform: scale(0.95);
      }
    </style>
    <button id="customThemeBtn" title="切换主题">
      <i class="fas fa-moon" id="customThemeIcon"></i>
    </button>
    `;
    document.body.appendChild(div);
    
    var btn = document.getElementById('customThemeBtn');
    var icon = document.getElementById('customThemeIcon');
    
    function updateIcon(isDark) {
      icon.className = isDark ? 'fas fa-sun' : 'fas fa-moon';
    }
    
    // 检查当前主题
    var savedTheme = localStorage.getItem('theme');
    var currentDark = savedTheme === 'dark' || (!savedTheme && document.body.classList.contains('dark'));
    updateIcon(currentDark);
    
    btn.onclick = function() {
      var isDark = document.body.classList.contains('dark') || 
                   document.documentElement.getAttribute('data-theme') === 'dark';
      
      if (isDark) {
        document.body.classList.remove('dark');
        document.documentElement.setAttribute('data-theme', 'light');
        localStorage.setItem('theme', 'light');
        updateIcon(false);
      } else {
        document.body.classList.add('dark');
        document.documentElement.setAttribute('data-theme', 'dark');
        localStorage.setItem('theme', 'dark');
        updateIcon(true);
      }
    };
  }
  
  // 刷新按钮
  function addRefreshButton() {
    var navbar = document.querySelector('.navbar-nav, .nav-links, .links');
    if (!navbar || document.querySelector('.refresh-btn')) return;
    
    var li = document.createElement('li');
    li.className = 'refresh-btn nav-item';
    li.innerHTML = '<a href="javascript:location.reload()" title="刷新"><i class="fas fa-sync-alt"></i></a>';
    li.style.cssText = 'margin-left:auto;';
    navbar.appendChild(li);
  }
  
  if (document.readyState === 'loading') {
    document.addEventListener('DOMContentLoaded', function() {
      setTimeout(createThemeToggle, 500);
      setTimeout(addRefreshButton, 800);
    });
  } else {
    setTimeout(createThemeToggle, 500);
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
