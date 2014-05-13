---
title: 'Debugging Dynamically Loaded JS with sourceURL'
author: Nathan Smith
tags: [JavaScript, sourceURL, AJAX, sourceMap]
---

I'll start with a short story.  About 2 years ago, I started working on a configurable dashboard application that allowed the user to choose and create "widgets".  Widgets consisted of arbitrary JavaScript that could (optionally) use a front-end API to interact with other widgets and control their display area.  The widget code is stored in the database and non-editable widgets are drawn from JS files.  The problem came with the fact that these widgets were allowed to dynamically import other libraries and stylesheets by essentially appending to the head after the page had loaded.

Any dynamically loaded script was automatically run through ``eval()`` and then the source discarded since it wasn't included on load.  If an error or exception occured in these scripts, the traceback would contain a cryptic error location and no ability to inspect or place breakpoints in the problematic location.  When I first discovered this "feature", I googled around looking for a solution but did not find anything that was satisfactory.  I gave up at the time but then I recently discovered ``sourceURL``.

Using ``sourceURL`` ([Chrome Dev Tools docs](https://developers.google.com/chrome-developer-tools/docs/javascript-debugging#breakpoints-dynamic-javascript)) allows a developer to give a "filename" to any dynamically loaded script (or any evaled code in general) so that it is kept around in the source listings for debugging.  It is kinda like a simplified version of a ``sourceMap`` which I'll get to in a minute.  To use ``sourceURL`` add the following at the end of the file (or code snippet):

~~~~ {.javascript}
//# sourceURL=script_filename.js
~~~~

With that line added (replace "script_filename") it will now appear in the JS debugger (Chrome Dev Tools, Firebug, etc.) and allows for breakpoints and meaningful stacktraces.

[Source Maps](https://developers.google.com/chrome-developer-tools/docs/javascript-debugging#source-maps) are the next step after ``sourceURL`` and allows minified, compiled, concatinated, etc JS to be mapped back to it's original source.  Some debuggers even allow mapping back to CoffeeScript.  Source maps are created by automated tools such as Closure Compiler or UglifyJS 2.0.  I have not messed with source maps yet, but it is good option to keep in mind when my development code shifts into an production environment.












