---
layout: default
title: Home
permalink: /
---

<section class="hero">
  <p class="hero__prompt"><span class="prompt">guest@{{ site.title }}</span><span class="path">:~$</span> whoami</p>
  <h1 class="hero__name">Nishant Ajit</h1>
  <p class="hero__tagline"></p>

  <p class="hero__prompt"><span class="prompt">guest@{{ site.title }}</span><span class="path">:~$</span> cat about.txt | head -n 3</p>
  <p>
    Welcome to my site! I'm a 4th year computer science student specializing in IoT studying in VIT Vellore. 
    To learn more about me, visit <a href="{{ '/about/' | relative_url }}">~/about</a>
  </p>
</section>

<section class="recent-posts">
  <p class="section-label">[recent posts]</p>
  {% if site.posts.size > 0 %}
  <ul class="post-list">
    {% for post in site.posts limit: 5 %}
    <li class="post-list__item">
      <span class="post-list__date">{{ post.date | date: "%Y-%m-%d" }}</span>
      <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
    </li>
    {% endfor %}
  </ul>
  <p><a href="{{ '/blog/' | relative_url }}">&gt; view all posts</a></p>
  {% else %}
  <p class="muted">No posts yet &mdash; write one in <code>_posts/</code>. See GUIDE.md.</p>
  {% endif %}
</section>
