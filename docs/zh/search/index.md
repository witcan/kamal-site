---
title: 搜索结果
nav: false
search: false
lang: zh
---

<h1 id="search-query">搜索结果：<mark></mark></h1>

<div id="search-results"></div>

<script>
  window.store = {
{% assign pages = site.pages | where: 'search', true %}
{% for page in pages %}
  {% if page.path contains 'docs/zh/' %}
    {% unless page.path contains 'docs/zh/search/' %}
      "{{ page.url | slugify }}": {
        "title": "{{ page.title | smartify | xml_escape }}",
        "content": {{ page.content | markdownify | strip_html | normalize_whitespace | jsonify }},
        "section": "{{ page.url }}".split("/").filter(element => element !== "").slice(2).join("/"),
        "url": "{{ page.url | xml_escape }}"
      }{% unless forloop.last %},{% endunless %}
    {% endunless %}
  {% endif %}
{% endfor %}
  };
</script>
