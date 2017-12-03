---
layout: default
permalink: /projects/index.html
title: Projects
page: projects
---

<ul class="post-list">

    {% assign projects = site.projects | sort:"date" | reverse %}

    {% for project in projects %}
        {% if project.featured %}
            {% include project_preview.md project=project %}
        {% endif %}
    {% endfor %}

</ul>

<div class="show_more">
    <a href="/projects/all">
        Show All
    </a>
</div>

<!-- <p class="rss-subscribe">subscribe <a href="{{ "/feed.xml" | relative_url }}">via RSS</a></p> -->

