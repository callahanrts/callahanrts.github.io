
<li class='project'>
    <h3 class='font-weight-normal'>
        <a href="{{ project.url | relative_url }}">
            {{ project.name | escape }}
        </a>
    </h3>

    <p class="date">
        {% assign date_format = site.minima.date_format | default: "%b %Y" %}
        <span>
            {{ project.date | date: date_format }}
        </span>
    </p>

    <p>{{ project.excerpt }}</p>
</li>
