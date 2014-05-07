---
layout: post
title: "Running Express 4 Sites On Azure"
date: 2014-05-07 06:57:05 -0700
comments: true
categories: Node Express Azure
---

If you've created an Express 4 app with the new scaffold utility recently, you may have noticed that it doesn't work in Azure. When you try to load the site, it fails with the following error:

> The page cannot be displayed because an internal server error has occurred.

The Diagnosis
-------------

This is because of the new structure Express creates for its apps. Instead of app.js being the expected entry point, Express now specifies bin/www as the entry point for starting the server.

I suspect the reason for this is so that the newly scaffolded app can be consumed as a module, allowing the consumer to worry about port binding etc. Unfortunately, this causes problems for Azure.

When Azure starts a Node.js site, it looks for either a server.js or an app.js. As far as I can tell from my admittedly limited testing, it doesn't seem look at package.json for an alternate entry point. Because of this, when it includes app.js, nothing ever binds to the listening port, so the app immediately exits on startup.

The Workaround
--------------

Since Azure first looks for a server.js file when it starts a site, the easiest solution I've found is to add a `server.js` file with the following contents.

{% codeblock lang:javascript %}
require('./bin/www');
{% endcodeblock %}

Now, when Azure starts your site, it will require server.js, which will require bin/www, which will require app.js. After that somewhat convoluted redirection chain, all will again be right in the world.

The Azure documentation has an [alternative workaround](http://azure.microsoft.com/en-us/documentation/articles/cloud-services-nodejs-develop-deploy-express-app/) (see steps 4 and 5 of "Install Express"), but I don't care for it.
The problem with their solution is that it duplicates the code from bin/www into server.js, which means any updates you make to bin/www will also need to be applied to server.js.
Long story short, code duplication sucks.

The Solution
------------

There are a couple of things that would properly solve this problem instead of just working around it.

### Express Scaffold Fix

There are probably a few ways Express could fix this, but here's the simplest I can think of:

* Scrap the funny business with app.js and bin/www.
* Put the main server logic in index.js.
  If you want this code to be consumable as a module, make it as simple as possible by putting the module entry point at the most logical place (index.js).
* Move bin/www to app.js.
  Since that was the entry point in Express 3.x, it is presumably safest to keep that as the entry point.

### Azure Fix

In my oppinion, Azure should have a way to specify the entry-point in package.json rather than hardcoding to app.js and server.js.

The conventional `npm start` configuration is a logical place to begin, since it's a [supported spec from npm](https://www.npmjs.org/doc/misc/npm-scripts.html).

```
{
    "scripts": {
        "start" : "<path>"
    }
}
```

Azure relies on iisnode, which probably can't consume package.json directly.
However, Azure could look at a setting in package.json during the site setup process (when it does stuff like package restore), and configure web.config, just like it does now to switch between app.js and server.js.
