---
title: 'Underscore.js Throttle Function'
author: Nathan Smith
tags: [JavaScript, Underscore.js, Backbone.js, throttle, functional programming]
---

[Underscore.js](http://underscorejs.org/) is a JavaScript library that I have come to greatly appreciate since I first discovered it with [Backbone.js](http://backbonejs.org/).  Underscore is a collection of functional programming helpers packed with goodies like each, map, reduce, find, filter, every, some, contains, pluck, sample, flatten, union, intersection, difference, uniq, zip, range, bind, delay, defer, throttle, debounce, once, after, wrap, keys, values, extend, pick, tap, has, times, random, template.  The functions I listed are just a few that I have used and there are many, many more.  As an added bonus, many of these can be chained together to drastically improve readability and compactness.

One function that I used recently for the first time is [throttle](http://underscorejs.org/#throttle).  I was creating an interface that essentially consisted of many dynamically created checkboxes that toggle features on a map.  The problem was that each toggling would create a server request that depended on the combined states of the checkboxes.  My views were set up to respond to each individual toggling, but there was also a "check all" button that would toggle them all simultaneously.  Obviously I did not want potentially hundreds of requests to be sent when one would suffice.  I wanted them map to be responsive, but not to create multiple unnecessary requests.

Enter throttle.  Instead of each toggling creating a request, it would call a throttled function.  Throttle accepts a function, delay time and some options.  For this case I chose:

~~~~ {.javascript}
var updateMap = _.throttle(function() {
    // Do stuff...
}, 100, {leading: false});
~~~~

This returns a wrapped function that will accept (& ignore) calls, then call the supplied functions after 100ms.  It will continue this behavior until it is no longer called within the specified amount of time, in which case it will wait until it is called again.  The ``{leading: false}`` part tells it to not call the function on the leading edge.  By default it will call the function at the beginning of the first cycle and at the end of that and every subsequent cycle.  The ``trailing`` call can also be disabled if desired.

In the end it gave me a responsive solution that elegantly handled the multiple simultaneous requests edge case.













