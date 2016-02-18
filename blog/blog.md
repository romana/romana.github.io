---
layout: page
title: Welcome to the Romana Blog
menu_text: Blog
nav_text: Blog
icon: content_copy
categories:
- blog
firstnav: 6
secondnav: 9
permalink: /blog/ 
---

<div class="page-content">
  

{% for post in site.categories['blog'] %}

    <div class="mdl-card mdl-shadow--2dp mdl-cell mdl-cell--8-col mdl-cell--8-col-desktop mdl-cell--4-col-tablet  mdl-cell--4-col-phone">
      <div class="mdl-card__title" {% if post.image %} style="background: url('{{ post.image }}') center/cover;" {% endif %}>
        <h2 class="mdl-card__title-text">{{ post.title }}</h2>
      </div>
      <div class="mdl-card__supporting-text">
        <span>{{ post.date | date: "%b %-d, %Y" }}</span>
        <p>{{ post.excerpt }}</p>
      </div>
      <div class="mdl-card__actions mdl-card--border">
        <a class="mdl-button mdl-button--colored mdl-js-button mdl-js-ripple-effect" href="{{ post.url | prepend: site.baseurl }}">
          Read More
        </a>
      </div>
    </div>

    {% endfor %}

</div>
