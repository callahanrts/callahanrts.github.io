<!DOCTYPE html>
<html lang="{{ page.lang | default: site.lang | default: "en" }}">

  {% include head.html %}

  <body>

    <div class="parallax-container">
      <div class="parallax"><img src="/assets/tahoe.jpg"></div>
      {% include header.html %}
    </div>

    {{ content }}

    {% include footer.html %}

  </body>

  <script>
  $('.parallax').parallax();
  </script>

</html>

