---
layout: page
title: Posts
permalink: /posts/
---

<h1>All Posts</h1>

<div id="tag-filter">
  <label for="tag-select">Filter by tag:</label>
  <select id="tag-select">
    <option value="">All tags</option>
    {% assign tags = site.tags | sort %}
    {% for tag in tags %}
      <option value="{{ tag[0] }}">{{ tag[0] }}</option>
    {% endfor %}
  </select>
</div>

<ul id="post-list">
  {% for post in site.posts %}
    <li class="post-item" data-tags="{{ post.tags | join: ',' }}">
      <h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
      <span class="post-meta">{{ post.date | date: "%B %d, %Y" }}</span>
      <div class="post-tags">
        {% for tag in post.tags %}
          <span class="tag">{{ tag }}</span>
        {% endfor %}
      </div>
      <p>{{ post.excerpt | strip_html | truncatewords: 50 }}</p>
    </li>
  {% endfor %}
</ul>

<script>
document.getElementById('tag-select').addEventListener('change', function() {
  var selectedTag = this.value;
  var posts = document.getElementsByClassName('post-item');
  
  for (var i = 0; i < posts.length; i++) {
    var post = posts[i];
    var postTags = post.getAttribute('data-tags').split(',');
    
    if (selectedTag === '' || postTags.indexOf(selectedTag) !== -1) {
      post.style.display = '';
    } else {
      post.style.display = 'none';
    }
  }
});
</script>