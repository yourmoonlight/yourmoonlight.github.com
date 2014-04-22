---
layout: page
title: Hello World!
tagline: Supporting tagline
---
{% include JB/setup %}

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>


<!-- Pagination links -->
<div class="pagination pagination-centered">
  <ul>
  {% if paginator.previous_page %}
    {% if paginator.previous_page == 1 %}
      <li class="prev"><a href="/" title="Previous page">Prev</a></li>
    {% else %}
      <li class="prev"><a href="/page{{paginator.previous_page}}" title="Previous page">Prev</a></li>
    {% endif %}
  {% else %}
    <li class="prev disabled"><a>Prev</a></li>
  {% endif %}
  {% if paginator.next_page %}
    <li class="next"><a href="/page{{paginator.next_page}}" title="Next page">Next</a></li>
  {% else %}
    <li class="next disabled"><a>Next</a>
  {% endif %}
  </ul>
</div>

