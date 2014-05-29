---
layout: page
title: "Node Modules"
date: 2014-05-29 00:54
comments: true
sharing: true
footer: true
---

I've written a number of few Node.js modules for performing basic tasks.
Most are CLI modules that are intended to be installed with the `-g` flag.

web-server-cli
--------------

I frequently find myself wanting to statically serve a folder on my machine for debugging purposes.
Having to configure Apache/nginx/IIS to serve the folder is usually more effor than I want to mess with.
web-server-cli solves this by spinning up a static Express server in the current folder.

```
$ web-server
Static server started at http://localhost:8000/
```

There are configuration options for the root directory and the port.
They're documented on the [web-server-cli package page](https://www.npmjs.org/package/web-server-cli).

nodeignore
----------

I spin up a lot of new git repositories in my day-to-day work, so I frequently need to configure .gitignore files.
GitHub has [a great .gitignore repo](https://github.com/github/gitignore) with configurations for various languages and frameworks.
Unfortunately, it can still be a hassle to navigate to the repo, find the language I want, and copy the contents into a local .gitignore file.
That's where nodeignore comes in.

```
$ nodeignore
Downloading gitignore to .gitignore
Finished downloading .gitignore
```

By default, nodeignore downloads [the Node.js .gitignore file](https://github.com/github/gitignore/blob/master/Node.gitignore) (thus the name).
You can download any .gitignore definition in the [github/gitignore](https://github.com/github/gitignore) repo with the <code>&#8209;l</code> parameter.

```
nodeignore -l VisualStudio
```

There are also configuration options for appending/replacing existing .gitignore files.
Full instructions for configuration options are listed on the [nodeignore package page](https://www.npmjs.org/package/nodeignore).

Unfortunately there's no support yet for downloading the "global" gitignore files, but it's a planned feature.

Placeholder Images
------------------

There are a number of services that provide an API for generating placeholder images with specific dimensions.
These are great for hotlinking into website designs, but if you need to download a bunch of different resolutions, the process becomes more tedious.
That's why I created npm packages for downloading placeholder images from the CLI.

For starters, there's a package that contains the generic logic for downloading images from a placeholder website: [placebase](https://www.npmjs.org/package/placebase).

There are a number of websites that provide placeholder images.
The following packages harness "placebase" to download images from their respective websites.

* [placekitten](https://www.npmjs.org/package/placekitten)
* [placebear](https://www.npmjs.org/package/placebear)
* [placeholdit](https://www.npmjs.org/package/placeholdit)
* [placecage](https://www.npmjs.org/package/placecage)
* [fillmurray](https://www.npmjs.org/package/fillmurray)

Each module has basically the same configuration settings.
Eventually I'll also add flags for downloading grayscale images etc.