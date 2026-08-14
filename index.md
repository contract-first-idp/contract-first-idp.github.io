---
layout: default
title: Contract-First IDP
---

<section class="cf-hero">
  <div>
    <div class="cf-eyebrow">OpenShift reference architecture · v1.0.0</div>
    <h1>Build around the <span class="cf-gradient-word">contract.</span></h1>
    <p class="cf-lede">A working reference architecture for contract-first delivery: design and publish an API independently, build providers and consumers against it, then release and promote through reviewable Git state.</p>
    <div class="cf-actions">
      <a class="cf-btn cf-btn-primary" href="{{ "/2026/08/13/Contract-First-IDP-v1.html" | relative_url }}">Read the v1.0 story</a>
      <a class="cf-btn" href="#architecture">Explore the architecture</a>
    </div>
    <div class="cf-hero-note">Developer Hub captures intent · Git records it · Argo CD reconciles it</div>
  </div>

  <div class="cf-lifecycle-wrap" aria-label="Contract-first lifecycle">
    <div class="cf-lifecycle">
      <div class="cf-life-ring cf-consumer">
        <div class="cf-ring-inner">
          <div class="cf-ring-icon cf-person-icon" aria-hidden="true"></div>
          <div class="cf-ring-title">Consumer</div>
        </div>
      </div>
      <div class="cf-life-ring cf-producer">
        <div class="cf-ring-inner">
          <div class="cf-ring-icon cf-service-icon" aria-hidden="true"></div>
          <div class="cf-ring-title">Producer</div>
        </div>
      </div>
      <div class="cf-life-ring cf-contract">
        <div class="cf-ring-inner">
          <div class="cf-ring-icon cf-doc-icon" aria-hidden="true"></div>
          <div class="cf-ring-title">Contract</div>
        </div>
      </div>
    </div>
  </div>
</section>

<section class="cf-section" id="architecture">
  <div class="cf-section-head">
    <h2>Reference architecture, expressed in three parts.</h2>
    <p>Contract-First IDP is published as a set of complementary reference artifacts. Together they show how the developer experience, trusted runtime implementation, and shared platform can fit together while keeping Git as the durable contract between layers.</p>
  </div>

  <div class="cf-arch-grid">
    <a class="cf-arch-card" href="https://github.com/contract-first-idp/software-templates">
      <h3>Developer experience reference</h3>
      <p>Golden paths show how Developer Hub can capture catalog relationships and lifecycle intent as reviewable Git changes for APIs, Components, Resources, activation, and promotion.</p>
      <div class="cf-arch-meta"><span class="cf-dot"></span> Backstage → tenant Git</div>
      <div class="cf-repo-name">software-templates</div>
    </a>
    <a class="cf-arch-card" href="https://github.com/contract-first-idp/developer-charts">
      <h3>Runtime implementation reference</h3>
      <p>Reusable charts show how reviewed tenant intent can be interpreted into namespaces, API publication, builds, workloads, releases, and managed resources.</p>
      <div class="cf-arch-meta"><span class="cf-dot"></span> tenant Git → desired state</div>
      <div class="cf-repo-name">developer-charts</div>
    </a>
    <a class="cf-arch-card" href="https://github.com/contract-first-idp/platform-components">
      <h3>Platform installation reference</h3>
      <p>The OpenShift reference target demonstrates the shared services and controllers that support the lifecycle: GitOps, Pipelines, Developer Hub, Dev Spaces, Quay, Registry, Microcks, identity, secrets, and operators.</p>
      <div class="cf-arch-meta"><span class="cf-dot"></span> Argo CD → OpenShift</div>
      <div class="cf-repo-name">platform-components</div>
    </a>
  </div>
</section>

<section class="cf-section cf-blog-section" id="blog">
  <div class="cf-section-head cf-blog-head">
    <div>
      <h2>Latest from the blog</h2>
      <p>The architecture has been developed in the open. The older essays explain the principles; the v1.0.0 release shows where those ideas landed in a working reference implementation.</p>
    </div>
    <a class="cf-view-all" href="{{ "/archive.html" | relative_url }}">View all posts →</a>
  </div>

  {% assign featured = site.posts.first %}
  <a class="cf-featured" href="{{ featured.url | relative_url }}">
    <div class="cf-featured-copy">
      <div>
        <span class="cf-post-label">Featured · {{ featured.tags | first | default: "Article" }}</span>
        <span class="cf-post-date">{{ featured.date | date: "%B %Y" }}</span>
      </div>
      <h3>{{ featured.title }}</h3>
      <p>{{ featured.description }}</p>
      <div class="cf-read-link">Read the article <span>→</span></div>
    </div>
    <div class="cf-featured-art" aria-hidden="true">
      <div class="cf-mini-rings"><i></i><i></i><i></i></div>
    </div>
  </a>

  <div class="cf-post-grid">
    {% for post in site.posts offset:1 limit:3 %}
      <a class="cf-post-card" href="{{ post.url | relative_url }}">
        <time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%B %Y" }}</time>
        <h4>{{ post.title }}</h4>
        <p>{{ post.description | default: post.excerpt | strip_html | truncatewords: 24 }}</p>
        <span class="cf-post-arrow">Read article →</span>
      </a>
    {% endfor %}
  </div>
</section>
