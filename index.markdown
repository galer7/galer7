---
layout: home
---

# Welcome to Gabriel Galer's Portfolio

I'm a Software Engineer specializing in distributed systems and web development. With experience in TypeScript, Node.js, and cloud technologies, I'm passionate about building scalable and efficient solutions.

## Featured Projects

{% for project in site.data.projects limit:3 %}
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

<a href="/projects" class="btn">View all projects</a>