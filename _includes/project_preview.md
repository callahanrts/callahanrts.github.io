
<li class='project'>
    <a href="{{ project.url | relative_url }}">
        <h3 class='font-weight-normal'>
            {{ project.name | escape }}
        </h3>
    </a>

    <p class="date">
        {% assign date_format = site.minima.date_format | default: "%b %Y" %}
        <span>
            {{ project.date | date: date_format }}
        </span>
    </p>

    <p>{{ project.excerpt }}</p>
</li>
