---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: default
---

<ul>
  {% for blog in site.blogs %}
    <li>
        {% if blog.excerpt %}
            {{ blog.excerpt }}
            {{ blog.date | date: "%-d %B %Y" }}  
            </br>
        {% endif %}
        <a href="{{ blog.url }}">{{ blog.title }}</a>
    </li>
    <hr>
  {% endfor %}
</ul>

