---
layout: home
title: Home
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

## Recent Posts

{% for post in site.posts limit:3 %}
  <h3><a href="{{ post.url }}">{{ post.title }}</a></h3>
  <p>{{ post.excerpt | strip_html | truncatewords: 30 }}</p>
{% endfor %}

[View all posts](/posts)