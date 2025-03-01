---
layout: default
title: JW | projects
---
# My Projects

<span style="color: #FF6772">[Projects Under Development]</span>

<div class="project-list">
  {% assign categories = site.projects | group_by: "category" | sort: "name" %}
  {% for category in categories %}
    <div class="category-row">
      <h2 class="category-heading">{{ category.name }}</h2>
      <div class="projects-container">
        {% for project in category.items %}
          <div class="project">
            <h3>{{ project.title }}</h3>
            <p>{{ project.description | default: "No description available" }}</p>
            {% if project.url %}
              <a href="{{ project.url }}">View</a>
            {% endif %}
          </div>
        {% endfor %}
      </div>
    </div>
  {% endfor %}
</div>