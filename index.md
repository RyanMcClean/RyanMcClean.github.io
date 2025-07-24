---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: default
---

<ul>
  {% for blog in site.blogs %}
    <li>
    {{ blog.date }}
        {% if blog.excerpt %}
            {{ blog.excerpt }}
        {% endif %}
        <a href="{{ blog.url }}">{{ blog.title }}</a>
    </li>
    <hr>
  {% endfor %}
</ul>

