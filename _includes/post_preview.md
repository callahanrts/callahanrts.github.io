
<li class='project'>
    <h3 class='font-weight-normal'>
        <a href="{{ post.url | relative_url }}">
            {{ post.title | escape }}
        </a>
    </h3>

    <p class="date">
        <span>
            {{ post.date | date: site.minima.date_format }}
        </span>
    </p>

    <p>{{ post.excerpt }}</p>
</li>
