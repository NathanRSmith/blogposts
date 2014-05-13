---
title: 'Underscore.js Debounce Function'
author: Nathan Smith
tags: [JavaScript, Underscore.js, Backbone.js, debounce, functional programming]
---

Recently I wrote about the [Underscore.js](http://underscorejs.org/) library and how it has many useful functions for JavaScript development.  I also discussed the [throttle](http://underscorejs.org/#throttle) function in particular and how it is great for preventing extra server requests, but still keeping applications responsive.  This time I will briefly discuss the [debounce](http://underscorejs.org/#debounce) function.

I have used debounce many times.  It is great for preventing multiple unintentional calls (like preventing accidentable double-clicks to a "submit" button) or waiting to execute some code til the user is done inputting information.  Debounce accepts a function, wait time, and optionally a boolean to indicate if the function should be called on the leading or trailing edge.  By default the ``immediate`` argument is false and the function will execute on the trailing edge.

Let's pretend we have a map that we want to be rerendered when the browser window is resized.  The basic functionality is easy, just listen to the ``resize`` event on the window object and call ``map.render`` or ``map.updateSize``.  But the problem with this approach is that the resize event is triggered for every incremental resizing while the user is dragging the window edge.  This will result in the map rerendering hundreds of time, with potentially hundreds of requests and lots of unnecessary work.  The interface will lag and lead to a poor user experience.  But simply by using:

~~~~ {.javascript}
var lazyUpdateMapSize = _.debounce(map.updateSize, 300);
$(window).resize(lazyUpdateMapSize);
~~~~

the map will wait to rerender until the window has stopped being resized for at least 300ms.  This will lead to a smoother interface that will only execute this potentially computationally expensive piece of code when the user is finished (or paused) rearranging their workspace.













