---
layout: page
title: Blog
excerpt: "An archive of blog posts sorted by date."
search_omit: true
---

The Framework is built in Python using the Eclipse Integrated Development Environment (IDE) and postreSQL as database. The setup of the IDE with all its components is covered in the [Setup IDE Blog](https://karttur.github.io/setup-ide/). If you want to setup Karttur's GeoImagine Framework you have to start with building the Eclipse Spatial Data IDE (SPIDE).

<ul class="post-list">
{% for post in site.categories.libspectrosupport %}
  <li><article><a href="{{ site.url }}{{ post.url }}">{{ post.title }} <span class="entry-date"><time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%B %d, %Y" }}</time></span>{% if post.excerpt %} <span class="excerpt">{{ post.excerpt | remove: '\[ ... \]' | remove: '\( ... \)' | markdownify | strip_html | strip_newlines | escape_once }}</span>{% endif %}</a></article></li>
{% endfor %}
</ul>
