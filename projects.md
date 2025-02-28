---
layout: default
title: My Projects
---
# My Projects

<div class="project-list">
  {% for project in site.projects %}
    <div class="project">
      <h2>{{ project.title }}</h2>
      <p>{{ project.description | default: "No description available" }}</p>
      {% if project.url %}
        <a href="{{ project.url }}">View</a>
      {% endif %}
    </div>
  {% endfor %}
</div>