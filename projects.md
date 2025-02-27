---
layout: default
title: Projects
---
<h2>My Projects</h2>
<ul>
  {% for project in site.projects %}
    <li>
      <strong>{{ project.title }}</strong>: {{ project.description | default: "No description available" }}
      {% if project.url %}
        <a href="{{ project.url }}">View</a>
      {% endif %}
    </li>
  {% endfor %}
</ul>