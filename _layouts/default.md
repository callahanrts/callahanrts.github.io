<!DOCTYPE html>
<html lang="{{ page.lang | default: site.lang | default: "en" }}">

  {% include head.html %}

  <body>

    {% include header.html %}

    {{ content }}

    {% include footer.html %}

    {% include async_styles.html %}
  </body>

</html>

