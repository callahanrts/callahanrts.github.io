---
layout: project
name:   massive-list
title:  massive-list
date:   2016-02-12
github: "callahanrts/massive-list"
categories: javascript travisci coveralls
image: /assets/projects/massivelist-promo.png
description: >
    A library to quickly create a massive list when things like knockout's foreach are too slow. To
    see MassiveList in action, checkout my other project beatcabinet.
---
A library to quickly create a massive list when things like knockout's foreach are too slow. To
see MassiveList in action, checkout my other project beatcabinet.

While working on beatcabinet, I found that Knockout's foreach was too sluggish adding songs to
the queue. I did a quick performance test between Angular's ng-repeat, Knockout's foreach, jQuery,
and plain javascript. I found that for simply adding rows to a list, plain javascript was by far
the fastest. I created MassiveList as a plain javascript implementation for manipulating a really
large (thousands) list of elements.


#### Example Usage:
```javascript
// Initializing a massive list with no data
// MassiveList(id, [, options])
var list = MassiveList("element-id");

var element = {
  id: "elementId",
  className: "list of classes",
  content: "<p>html content</p>",
  events: {
    click: function(){
      console.log("click");
    },
    dblclick: function{
      console.log("double click");
    }
    ...
  }
}

// Inserting a row to the end of the list
list.push(element [, tag])

// Inserting a row at the beginning of the list
list.unshift(element)

// Insert an array of rows into the list
list.push(array)

// Change the value at a single location
list.set(index, element)

// Get the value at a single location
var element = list.get(index)

// Popping last row
var element = list.pop()

// Sort list elements
list.sort(function(a, b){
  // sort function
})

// splice(start, deleteCount[, item1[, item2[, ...]]])
// Remove existing and/or add elements
list.splice()
```
