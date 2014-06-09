---
layout: post
title: "Ending a Stream Without Consuming Its Data"
date: 2014-06-02 10:28:46 -0700
comments: true
categories: Node
---

I ran into an issue recently while writing [a node module](https://www.npmjs.org/package/placebase).
My process would sometimes complete its work, but wouldn't end.
After a bit of debugging, I figured out what was happening.

I had implemented a handler for the HTTP "location" header.
If a response came back with that header, I would ignore that response and go fetch the new location instead.
Unfortunately, whenever I did that, the process would finish fetching the new location, and then just hang there for about a minute bofore exiting.
The reason this happened was because I wasn't properly handling the first response.

Here's an oversimplified explanation of what happens.
This is from my limited understanding of Node internals, so the details may be slighly off, but the overall idea is correct.

When you use the "http" module to make a request in Node, the response it gives you is a stream.
If you don't do anything to consume that stream's data, Node doesn't exit because there's still work to be done (consuming the stream).
In order to get Node to exit without something clunky like process.exit(), you just need to switch the stream to "flowing" mode.

```javascript
var http = require('http');
 
var responseHandler = function(response) {
  if (response.headers.location) {
    http.get(response.headers.location, responseHandler);
    response.resume();
  } else {
    response.pipe(process.stdout);
  }
};
 
http.get(process.argv[2], responseHandler);
```

Notice on line 6 how I've called response.resume().
This switches the response stream to "flowing" mode.
According to the [streams documentation](http://nodejs.org/api/stream.html#stream_readable_resume),
this is the way to finish a stream if you don't care about consuming its data.