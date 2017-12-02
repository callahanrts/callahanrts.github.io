---
layout: post
title:  "Tiny Tour"
date:   2017-09-01
github: "callahanrts/tiny-tour"
categories: javascript interactive-tour
promo: /assets/projects/tiny-tour.png
---
<!--link rel="stylesheet" href="../tt/dist/material.min.css" id="material" />
<link rel="stylesheet" href="../tt/dist/tour.min.css">
<script src="../tt/dist/tour.min.js"></script>

<style>
.one {
  background-color: teal;
  position: absolute;
  top: 50px;
  left: 50px;
  width: 200px;
  height: 100px;
}

.two {
  background-color: red;
  position: absolute;
  top: 175px;
  left: 50px;
  width: 600px;
  height: 30px;
}

.three {
  background-color: blue;
  position: absolute;
  top: 50px;
  left: 300px;
  width: 300px;
  height: 40px;
}

.four {
  background-color: green;
  position: absolute;
  top: 115px;
  left: 325px;
  width: 225px;
  height: 50px;
}

.five {
  margin-top: 220px;
  margin-left: 40px;
  width: 300px;
  height: 150px;
  background-color: purple;
}
.demo {
  min-height: 300px;
  overflow: auto;
  position: relative;
  width: 100%;
}
</style>


fdslakj

## Basic Example

<button class="btn btn-primary" onclick="basicTour.start()">DEMO</button>
<div class='basic demo'>
  <div class='one'></div>
  <div class='two'></div>
  <div class='three'></div>
  <div class='four'></div>
</div>

```html
<div class='one'></div>
<div class='two'></div>
<div class='three'></div>
<div class='four'></div>
```
```javascript
tour = new Tour({
  padding: 0,
  next: 'More',
  done: 'Finito',
  prev: 'Less',
  tipClasses: 'tip-class active',
  steps: [
    {
      element: ".one",
      tip: "This box is tourqoise!",
      position: "right"
    },
    {
      element: ".two",
      tip: "Look how red this box is!",
      data: "Custom Data",
      position: "bottom"
    },
    {
      element: ".three",
      tip: "Almost too blue! Reminds of a default anchor tag.",
      position: "left"
    },
    {
      element: ".four",
      tip: "A fourth one to show the tip on all four sides.",
      position: "top"
    }
  ]
})
tour.start();
```

<script>
window.onload = function() {
  window.basicTour = new Tour({
    padding: 0,
    next: 'More',
    done: 'Finito',
    prev: 'Less',
    tipClasses: 'tip-class active',
    steps: [
      {
        element: ".basic .one",
        tip: "This box is tourqoise!",
        position: "right"
      },
      {
        element: ".basic .two",
        tip: "Look how red this box is!",
        data: "Custom Data",
        position: "bottom"
      },
      {
        element: ".basic .three",
        tip: "Almost too blue! Reminds of a default anchor tag.",
        position: "left"
      },
      {
        element: ".basic .four",
        tip: "A fourth one to show the tip on all four sides.",
        position: "top"
      }
    ]
  })
}
</script>

## Theme Examples
<button class="btn btn-primary material-btn" onclick="material()">MATERIAL</button>

<script>
window.materialTour = new Tour({
  padding: 0,
  tipClasses: 'tip-class active',
  steps: [
    {
      element: ".material-btn",
      tip: "This is what the Material theme looks like.",
      position: "right"
    },
    {
      element: ".material-btn",
      tip: "One more to show the prev button",
      position: "top"
    }
  ]
})

materialTour.override('end', function(self) {
  self();
  document.getElementById('material').disabled  = true;
})

window.material = function() {
  document.getElementById('material').disabled  = false;
  materialTour.start();
}
document.getElementById('material').disabled  = true;
</script>

## Using HTML inside a tip

<style>
h1.h1{ font-size: 1.3em; }
</style>
<button class="btn btn-primary html-btn" onclick="startHtmlTour()">DEMO</button>

<script type="text/javascript">
window.htmlTour = new Tour({
  padding: 0,
  tipClasses: 'tip-class active',
  steps: [
    {
      element: ".html-btn",
      tip: "<h1 class='h1'>Tip Headline</h1> You can use HTML to add a headline, or make things <b>bold</b>.",
      position: "right"
    }
  ]
})

window.startHtmlTour = function() {
  htmlTour.start();
}
</script>

```javascript
new Tour({
  padding: 0,
  tipClasses: 'tip-class active',
  steps: [
    {
      element: ".html-btn",
      tip: "<h1>Tip Headline</h1> You can use HTML to add a headline, or make things <b>bold</b>.",
      position: "right"
    }
  ]
})
```

## Scrolling in Between Tips

<button class="btn btn-primary scroll-btn" onclick="startScrollTour()">DEMO</button>

<script type="text/javascript">
  window.scrollTour = new Tour({
    padding: 0,
    tipClasses: 'tip-class active',
    steps: [
      {
        element: ".scroll-btn",
        tip: "Click next to scroll to the footer.",
        position: "right"
      },
      {
        element: "footer",
        tip: "Here's the footer tip. It will scroll back afterward",
        position: "top"
      }
    ]
  });

  scrollTour.override('nextStep', function(self) {
    document.documentElement.scrollTop = document.documentElement.offsetHeight
    self();
  });

  scrollTour.override('end', function(self) {
    var btn = document.getElementsByClassName('scroll-btn')[0];
    document.documentElement.scrollTop = document.documentElement.scrollTop + btn.getBoundingClientRect().top - 50;
    self();
  });

  window.startScrollTour = function(){
    scrollTour.start() ;
  };
</script>

```javascript
window.scrollTour = new Tour({
  padding: 0,
  tipClasses: 'tip-class active',
  steps: [
    {
      element: ".scroll-btn",
      tip: "Click next to scroll to the footer.",
      position: "right"
    },
    {
      element: "footer",
      tip: "Here's the footer tip. It will scroll back afterward",
      position: "top"
    }
  ]
});

scrollTour.override('nextStep', function(self) {
  document.documentElement.scrollTop = document.documentElement.offsetHeight
  self();
});

scrollTour.override('end', function(self) {
  var btn = document.getElementsByClassName('scroll-btn')[0];
  document.documentElement.scrollTop =
    document.documentElement.scrollTop + btn.getBoundingClientRect().top;
  self();
});
```

## Manipulating the DOM Between Tips

<style>
#surprise {
  border: 1px solid orange;
  padding: 15px;
  width: 100px;
  margin: 10px;
}
.dom-demo-container{ display: inline-block !important; margin: 10px;}

</style>
<div>
  <button class="btn btn-primary dom-btn" onclick="startDomTour()">DEMO</button>
  <div id="dom-demo-container"></div>
</div>
<br>

<script type="text/javascript">
  window.domTour = new Tour({
    padding: 0,
    steps: [
      {
        element: ".dom-btn",
        tip: "Click next to reveal a div",
        position: "right"
      },
      {
        element: "#surprise",
        tip: "This div was created on the fly between tips",
        position: "top"
      }
    ]
  });

  domTour.override('nextStep', function(self) {
    if(!document.getElementById('surprise')) {
      var container = document.getElementById("dom-demo-container");
      var el = document.createElement("div");
      el.innerText = "Surprise!";
      el.id = "surprise";
      container.appendChild(el);
    }
    self();
  });

  domTour.override('end', function(self) {
    self();
    document.getElementById("surprise").remove();
  })

  window.startDomTour = function(){
    domTour.start() ;
  };
</script>

```javascript
window.domTour = new Tour({
  padding: 0,
  steps: [
    {
      element: ".dom-btn",
      tip: "Click next to reveal a div",
      position: "right"
    },
    {
      element: "#surprise",
      tip: "This div was created on the fly between tips",
      position: "top"
    }
  ]
});

domTour.override('nextStep', function(self) {
  if(!document.getElementById('surprise')) {
    var container = document.getElementById("dom-demo-container");
    var el = document.createElement("div");
    el.innerText = "Surprise!";
    el.id = "surprise";
    container.appendChild(el);
  }
  self();
});
```

## Adding Animations
## Adding Extra CSS Classes
## Modifying a Tip Dynamically

-->
