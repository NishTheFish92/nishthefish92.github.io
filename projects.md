---
layout: page
title: Projects
permalink: /projects/
---

<p class="section-label">[portfolio]</p>

<div class="project-list">
{% for project in site.data.projects %}
  <div class="project-card">
    <h3 class="project-card__name">
      {% if project.url %}<a href="{{ project.url }}" target="_blank" rel="noopener noreferrer">{{ project.name }}</a>{% else %}{{ project.name }}{% endif %}
    </h3>
    <p class="project-card__description">{{ project.description }}</p>
    {% if project.tech %}
    <p class="project-card__tech">
      {% for t in project.tech %}<span class="tag">{{ t }}</span>{% endfor %}
    </p>
    {% endif %}
    {% if project.repo %}
    <p class="project-card__links"><a href="{{ project.repo }}" target="_blank" rel="noopener noreferrer">[source]</a></p>
    {% endif %}
  </div>
{% endfor %}
</div>
