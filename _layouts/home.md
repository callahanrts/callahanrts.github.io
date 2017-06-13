---
layout: default
---
<!--div class="home">
</div-->

<div class="section">
  <div class="row container">

    <main class="page-content" aria-label="Content">
      <div class="row">
        <div class="col s12">
          <ul class="tabs">
            <li class="tab col s6"><a href="#projects">Projects</a></li>
            <li class="tab col s6"><a href="#about">About</a></li>
            <!--li class="tab col s4"><a href="#resume">Resume</a></li-->
          </ul>
        </div>

        <div id="projects" class="col s12">
          {% include projects.md %}
        </div>

        <div id="about" class="col s12">
          {% include about.md %}
        </div>

        <!--div id="resume" class="col s12">
          {% include resume.md %}
        </div-->

      </div>
    </main>


  </div>
</div>

<script>
$(document).ready(function(){
  var tab = location.pathname.replace("/", "")
  $('ul.tabs').tabs({
    onShow: function(el){
      var path = el[0].id;
      history.pushState({}, path, "/"+path);
    }
  }).tabs("select_tab", tab);
});
</script>

