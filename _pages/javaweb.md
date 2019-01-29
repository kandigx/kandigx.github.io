---
layout: archive
permalink: /javaweb/
title: "Java Web Posts by Tags"
author_profile: true
header:
    #images: "/images/fort point.png"
---

{% include_cached base_path %}
{% include_cached group-by-array collection=site.posts field="tags" %}

{% for tag in group_names %}
  {% assign posts = group_items[forloop.index0] %}
  <h2 id="{{ tag | slugify }}" class="archive__subtitle">{{ tag }}</h2>
  {% for post in posts %}
    {% include_cached archive-single.html %}
  {% endfor %}
{% endfor %}