---
layout: page
title: Projects
permalink: /projects/
---

# My Projects

{% for project in site.data.projects %}
<div class="project">
  <h2>{{ project.name }}</h2>
  <p>{{ project.description }}</p>

  <div class="technologies">
    {% for tech in project.technologies %}
      <span class="tech-tag">{{ tech }}</span>
    {% endfor %}
  </div>
</div>
{% endfor %}