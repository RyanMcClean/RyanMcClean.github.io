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
        {% endif %}
        <br/>
        <br/>
    </li>
    <hr>
  {% endfor %}
</ul>

