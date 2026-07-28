---
title: "Posts"
layout: archive
permalink: /posts
author_profile: true
---

{% include base_path %}

<h3 class="archive__subtitle">{{ site.data.ui-text[site.locale].recent_posts | default: "All Posts" }}</h3>

{% for post in site.posts %}
  {% include archive-single.html %}
{% endfor %}