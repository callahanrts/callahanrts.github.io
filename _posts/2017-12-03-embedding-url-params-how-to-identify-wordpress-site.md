---
layout: post
comments: true
title: "Embedding, URL Params, and How to Identify Wordpress Sites"
date: 2017-12-03
categories:
---

Recently, I was embedding an iframe in a wordpress site and needed to pass down
some URL parameters into the iframe. The url the iframe needed was something
like `https://embedded-site.com?h=hello&w=world`. The parameters had to come from
my original page of `https://my-site.com?h=hello&w=world` because I needed to
share `https://my-site.com` and have something specific show up in the
`https://embedded-site.com` iframe.

The embed code for passing parameters from my parent page down to the iframe was
pretty simple:
```html
<iframe id="embed" data-href="https://my-site.com"></iframe>
<script type="text/javascript">
window.onload = function() {
    // Get a reference to the iframe
    var frame = document.getElementById("embed");

    // Load the iframe with the url parameters from the parent
    frame.href = frame.dataset.href + location.search;
}
</script>
```

Having all my code in place, I thought it was going to be smooth sailing from
here. I navigated my parent url with my desired parameters in place, and boom!
404 page. I spent forever trying to figure out why URL parameters would have
anything to do with the page being able to be found. Nothing made sense.

Finally, I figured out that the `&w=world` parameter was what was causing the
server to return a 404. It seems wordpress reserves the `w` parameter and
returns a 404 if it's used. From what I can tell, `w` is a query parameter in
wordpress, but I'm not exactly sure how it's used. If you know more about the
`w` parameter, please leave a comment and let me know.

So to fix the scenario where wordpress won't accept my `w` URL parameter that
needs to be passed to my iframe. I remapped the code to rename the variable.

```html
<iframe id="embed" data-href="https://my-site.com"></iframe>
<script type="text/javascript">
window.onload = function() {
    // Get a reference to the iframe
    var frame = document.getElementById("embed");

    // Load the iframe with the url parameters from the parent
    frame.href = frame.dataset.href + location.search.replace('wd', 'w');
}
</script>
```

Coincidentally, this also tought me a few more things about identifying
wordpress sites. Usually, a wordpress site can be identified by navigating to
`/wp-login.php` because most people don't take the time to rename their login
page. Today I learned that you can also throw `?w=random-string` into a
website and if you get a 404 in return, there's a good chance it's wordpress
denying you from using a reserved keyword as a URL parameter.
