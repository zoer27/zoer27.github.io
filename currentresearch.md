---
layout: default
title: Current research
---

<h1 class="mt-4">current research</h1>
{% assign current_research = site.current_research }
{% for pub in current_research %}
<div class="pubitem">
  <div class="pubtitle">{{ pub.title }}</div>
  <div class="pubsubtitle">{{ pub.subtitle }}</div>
</div>
{% endfor %}