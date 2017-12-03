---
layout: default
---

<article class="post" itemscope itemtype="http://schema.org/BlogPosting">

    <header class="post-header">
    <h2 class="post-title" itemprop="name headline">{{ page.title | escape }}</h2>
    <p class="post-meta">
        <time datetime="{{ page.date | date_to_xmlschema }}" itemprop="datePublished">
        {{ page.date | date: site.minima.date_format }}
        </time>
        {% if page.author %}
        • <span itemprop="author" itemscope itemtype="http://schema.org/Person"><span itemprop="name">{{ page.author }}</span></span>
        {% endif %}
    </p>

                <a href="http://github.com/{{ page.github }}" target="_blank" class="source">
                    <span class="fa fa-github fa-lg"></span>
                    <span>{{ page.github }}</span>
                </a>
    </header>

    <div class="post-content" itemprop="articleBody">
    {{ content }}
    </div>

    {% if site.disqus.shortname and page.comments == true %}
        {% include disqus_comments.html %}
    {% endif %}
</article>
