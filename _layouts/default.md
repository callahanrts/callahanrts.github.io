<!DOCTYPE html>
<html lang="{{ page.lang | default: site.lang | default: "en" }}">

  {% include head.html %}


<div class="fixed-top d-sm-none">
    <nav class="navbar navbar-light bg-white">
        <button class="navbar-toggler ml-auto bg-white" type="button" data-toggle="collapse" data-target="#navbarToggleExternalContent" aria-controls="navbarToggleExternalContent" aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
        </button>
    </nav>

    <div class="collapse bg-white" id="navbarToggleExternalContent">
        <div class="pr-4 pb-4">
            {% include nav-links.html %}
        </div>
    </div>
</div>

  <body>

    {% include header.html %}

    <div class="container">
        <div class="row">
            <div class="col-md-8 col-lg-7">
                {{ content }}
            </div>
        </div>
    </div>

    {% include footer.html %}

    {% include async_styles.html %}

    <script src="https://code.jquery.com/jquery-3.5.1.slim.min.js" integrity="sha256-4+XzXVhsDmqanXGHaHvgh1gMQKX40OUvDEBTu8JcmNs=" crossorigin="anonymous"></script>
    <script async defer src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.3/umd/popper.min.js" integrity="sha384-vFJXuSJphROIrBnz7yo7oB41mKfc8JzQZiCq4NCceLEaO4IHwicKwpJf9c9IpFgh" crossorigin="anonymous"></script>
    <script async defer src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0-beta.2/js/bootstrap.min.js" integrity="sha384-alpBpkh1PFOepccYVYDB4do5UnbKysX5WZXm3XxPqe5iKTfUKjNkCk9SaVuEZflJ" crossorigin="anonymous"></script>

    <!-- Place this tag in your head or just before your close body tag. -->
    <script async defer src="https://buttons.github.io/buttons.js"></script>
  </body>

</html>

