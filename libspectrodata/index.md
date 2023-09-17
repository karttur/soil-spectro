---
layout: page
title: OSSL spectral library data processing
excerpt: "OSSL spectral library data processing using python."
search_omit: true
---

## OSSL spectral library data processing

This page links to detailed instructions include a short introduction to OSSL with links to various resources. Then follows a step-by-step manual on retrieving data from OSSL. All the following posts build a continuing series on how to process the OSSL data using the OSSL python package, ending up in Machine Learning (ML) models using spectral data as covariates for prediciting different soil properties.

<ul class="post-list">
{% for post in site.categories.libspectrodata %}
  <li><article><a href="{{ site.url }}{{ post.url }}">{{ post.title }} <span class="entry-date"><time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%B %d, %Y" }}</time></span>{% if post.excerpt %} <span class="excerpt">{{ post.excerpt | remove: '\[ ... \]' | remove: '\( ... \)' | markdownify | strip_html | strip_newlines | escape_once }}</span>{% endif %}</a></article></li>
{% endfor %}
</ul>
