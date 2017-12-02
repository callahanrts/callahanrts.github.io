
<li class='project'>
    <a href="{{ post.url | relative_url }}">
        <h3 class='font-weight-normal'>
            {{ post.title | escape }}
        </h3>
    </a>

    <p class="date">
        {% assign date_format = site.minima.date_format | default: "%b %Y" %}
        <span>
            {{ post.date | date: date_format }}
        </span>
    </p>

    <p>{{ post.excerpt }}</p>
</li>
