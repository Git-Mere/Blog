---
title: "Hi Everyone!👋🏻"
permalink: /about/
layout: single
comments: false
---
![about_photo](https://git-mere.github.io/Blog/assets/images/about_photo.gif)

이것저것 한 번씩 해보는 거 좋아하고 게임하는 거 좋아하는 주니어 개발자입니다!

[EMAIL](meregi7410@gmail.com)

[WEBSITE](https://git-mere.github.io)

[GITHUB](https://github.com/Git-Mere)

### 👤 {{ site.author.name }}

📍 {{ site.author.location }}

💬 {{ site.author.bio }}

---

{% if site.author.links %}
### 🌐 Links

<ul>
  {% for link in site.author.links %}
    {% if link.url %}
    <li>
      <i class="{{ link.icon }}"></i>
      <a href="{% if link.label == 'Email' %}mailto:{{ link.url }}{% else %}{{ link.url }}{% endif %}" target="_blank">
        {{ link.label }}
      </a>
    </li>
    {% endif %}
  {% endfor %}
</ul>
{% endif %}