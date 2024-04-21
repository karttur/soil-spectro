---
layout: page
title: Chemometric soil modeling using soil spectral data
excerpt: "Description and illustation of a python process flow for going from raw soil spectral data to a chemometric soil propertries model using Machine Learning."
search_omit: true
---

## Chemometric modelling with spectral data

<ul class="post-list">
{% for post in site.categories.spectromodel %}
  <li><article><a href="{{ site.url }}{{ post.url }}">{{ post.title }} <span class="entry-date"><time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%B %d, %Y" }}</time></span>{% if post.excerpt %} <span class="excerpt">{{ post.excerpt | remove: '\[ ... \]' | remove: '\( ... \)' | markdownify | strip_html | strip_newlines | escape_once }}</span>{% endif %}</a></article></li>
{% endfor %}
</ul>
