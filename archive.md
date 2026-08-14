---
layout: default
title: Blog Archive
permalink: /archive.html
---

<section class="archive-hero">
  <div class="cf-eyebrow">Contract-First IDP Journal</div>
  <h1>Architecture developed in the open.</h1>
  <p>The blog follows the reference architecture from its early integration principles through the contract-first golden paths and the v1.0.0 implementation.</p>
</section>

<section class="archive-list" aria-label="Blog posts">
{% for post in site.posts %}
  <article class="archive-item">
    <time class="archive-date" datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%b %Y" }}</time>
    <div>
      <h2><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h2>
      {% if post.description %}<p>{{ post.description }}</p>{% endif %}
    </div>
  </article>
{% endfor %}
</section>
