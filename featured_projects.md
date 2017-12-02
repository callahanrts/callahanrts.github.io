---
layout: default
permalink: /projects/all/index.html
title: Projects
---

<div class="container">

    <div class="row">
        <div class="col-6">
            <ul class="post-list">

                {% assign projects = site.projects | sort:"date" | reverse %}

                {% for project in projects %}
                    {% include project_preview.md project=project %}
                {% endfor %}

            </ul>
        </div>
    </div>

    <p class="rss-subscribe">subscribe <a href="{{ "/feed.xml" | relative_url }}">via RSS</a></p>

</div>
