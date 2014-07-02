---
layout: post
title: "Azure Blob Storage and Node: All Together"
date: 2014-07-09 21:16:08 -0700
comments: true
categories: Azure Node
---

**This is part of a series on working with Azure Blob Storage in Node.**

1. [Introduction](/blog/2014/06/30/azure-blob-storage-and-node/)
1. [First Steps](/blog/2014/07/01/azure-blob-storage-and-node-first-steps/)
1. [Creating Blobs](/blog/2014/07/02/azure-blob-storage-and-node-creating-blobs/)
1. [Downloading Blobs](/blog/2014/07/03/azure-blob-storage-and-node-downloading-blobs/)
1. [Listing Blobs](/blog/2014/07/07/azure-blob-storage-and-node-listing-blobs/)
1. [Blob Metadata](/blog/2014/07/08/azure-blob-storage-and-node-blob-metadata/)
1. **All Together**

Building a Complete Service Over Azure Blob Storage

In the last post, we covered working with blob metadata.
In this final post, we'll talk about how all the previous pieces together to create a service that is backed by blob storage.

As a concrete example, we'll be implementing the image serving portion of [placebacon](http://placebacon.net/).
To keep things simple, we won't go into the Jade views, just the part that actually serves images.

### Creating an Node Server

We'll use Express to serve the images.
We could probably use something more lightweight, but Express is easy to use, so we'll use it.

First, let's create a package.json file so we can save our npm dependencies.

```bash
npm init
# Follow the prompts
# If in doubt, keep the defaults
# For "entry point", enter "server.js"
```

Next let's install some dependencies.

```bash
npm install --save express azure-storage debug
```

The "express" and "azure-storage" packages should be pretty self-explanatory.
The "debug" package lets us print debug output in a way that we can selectively turn on and off.
We'll talk about how to use "debug" in a bit.

When we initialized the package.json, we set server.js as the entry point.
Let's create that.
(Skip ahead if you're familiar with Express)

```javascript
var debug = require('debug')('placebacon:server'),
    storage = require('azure-storage'),
    http = require('http'),
    util = require('util'),
    express = require('express'),
    app = express();

app.set('port', process.env.PORT || 3000);

// TODO: add server routes

var server = app.listen(app.get('port'), function() {
    debug('Placebacon server listening on port %d', server.address().port);
});
```

We're requiring the "debug" package, then calling the function it exports with 'placebacon:server'.
This tells "debug" to only print log output if the DEBUG environment variable matches 'placebacon:server'.
For example:

```bash
node server.js
# [no output]

DEBUG=placebacon:server node server.js
# => Placebacon server listening on port 3000


DEBUG=placebacon:* node server.js
# => Placebacon server listening on port 3000
```

As you can see from the above examples, "debug" makes it easy to turn debugging statements on and off without modifying your code.

### Defining the Route

Next let's add a route. In the javascript snippet above, replace the "TODO" line with the following code.

```javascript
// Matches /100/200 -> 100x200 image
// Matches /100     -> 100x100 image
app.get('/:width/:height?', function(req, res) {
    var width = parseInt(req.params.width);
    var height = req.params.height ? parseInt(req.params.height) : width; 

    // If width or height isn't a number, go to next route
    if (isNaN(width) || isNaN(height)) {
        next();
        return;
    }

    var selectedImage = 0; // TODO: choose a random image
    var urlTemplate = 'http://placebacon.net/%d/%d?image=%d';
    var imageUrl = util.format(urlTemplate, width, height, selectedImage);


    debug('Fetching from %s', imageUrl);
    http.get(imageUrl, function(imageResponse) {
        if (200 !== imageResponse.statusCode) {
            debug('Unexpected response: %d - %s',
                  imageResponse.statusCode, imageResponse.statusMessage);
            res.send(503, imageResponse.statusMessage);
            return;
        } else {
            imageResponse.pipe(res);
        }
    }).on('error', function(err) {
        console.error('Error fetching from %s: %j', imageUrl, err);
        res.send(503, util.format("Error downloading %s: %s", imageUrl, e.message));
    });
});
```

At this point we have a fairly functional non-caching proxy.
Let's briefly go over the code.

First, the route is defined as `/:width/:height?`.
This tells Express that we want to match routes with one or two parts (the question mark makes the second part optional).
Those two parts will be mapped to `req.params.width` and `req.params.height`.
If the second part is left off, `req.params.height` will be undefined.

Inside our route handler, we first parse the width and height as integers.
If the height is missing, it's set to the width.
This allows users to use /100 as a shortcut for /100/100.

Next we check that width and height are actually integers.
If they aren't, we call "next()", to pass control onto the next route handler.
In this case, there isn't one, so Express will send a 404.

Next we select an image.
In this sample, it's hardcoded to zero, but normally we'll want to select a random image based on width and height.

Based on the image selection and resolution, we construct a URL to our backend.
In the sample it points at placebacon.com to make it easy to run the sample, but normally you'd point this at whatever service you're using to resize your images.
You'll also probably want to put the "urlTemplate" value in a config, but for the sake of code sample simplicity, it's hardcoded

Finally, we make an http request to the image url and pipe the response to our client.
The rest of the code is just error handling.

### Using Blob Storage
