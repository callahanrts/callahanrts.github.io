<div class="home">

  <ul class="post-list">
    {% for post in site.posts %}

      <li>
        <div class="col s12 m12">
          <div class="card horizontal">

            <div class="card-stacked">
              <div class="card-content">
                <a class="post-link" href="{{ post.url | relative_url }}">
                  <h4>{{ post.title | escape }}</h4>
                </a>
                <p class="date">
                  {% assign date_format = site.minima.date_format | default: "%b %Y" %}
                  <span class="post-meta">{{ post.date | date: date_format }}</span>
                </p>
                <p>{{ post.excerpt }}</p>
              </div>

              <div class="card-action">
                <!--a href="http://github.com/{{ post.github }}" target="_blank">
                  {% octicon mark-github height: 27 %}
                </a-->

                {% for category in post.categories %}
                  <div class="chip">{{ category }}</div>
                {% endfor %}


              </div>
            </div>

            {% if post.promo %}
            <div class="card-image">
              <img src="{{ post.promo }}">
            </div>
            {% endif %}

          </div>
        </div>
      </li>

    {% endfor %}
  </ul>

  <p class="rss-subscribe">subscribe <a href="{{ "/feed.xml" | relative_url }}">via RSS</a></p>

</div>
<!--
  <li>
    <h2>
      <a class="post-link" href="{{ post.url | relative_url }}">{{ post.title | escape }}</a>
    </h2>

    {% assign date_format = site.minima.date_format | default: "%b %-d, %Y" %}
    <span class="post-meta">{{ post.date | date: date_format }}</span>

    <div>{{ post.excerpt }}</div>
  </li>
  <hr size="1px">

-->
