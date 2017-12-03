
<li class='project'>
    <h3 class='font-weight-normal'>
        <a href="{{ project.url | relative_url }}">
            {{ project.name | escape }}
        </a>
    </h3>

    <p class="date">
        <span>
            {{ project.date | date: site.minima.date_format }}
        </span>
    </p>

    <p>{{ project.excerpt }}</p>
</li>
