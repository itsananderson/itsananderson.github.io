---
layout: post
title: "Testing Whether an NPM Module Is Installed"
date: 2014-03-29 10:12:23 -0700
comments: true
categories: Node NPM
---

Node doesn't have a well documented way of checking whether a module is available.
This is usually fine. You just include a package.json in your project, and expect people to run `npm install`.
This is how most popular Node frameworks work.

But what if you have a devDependency entry that you want to include only if it is installed?
I searched for an answer and didn't find anything conclusive, so I'm documenting what I *did* find in case I forget later.

Buried in NPM's package.json documentation is [an example](https://www.npmjs.org/doc/json.html#optionalDependencies)
for handling a package that may or may not be installed. In short, you use a try/catch. Here's an example:

```javascript
var program;

try {
    program = require('commander');
} catch (er) {
    // Any code to run if this module doesn't exist
}

if (program) {
    program.version('0.0.1') .option('-n, --name[name]', 'Your name')
         .parse(process.argv);
}
```