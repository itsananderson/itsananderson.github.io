---
layout: post
title: "Understanding Node's require Function"
date: 2014-10-12 09:24:25 -0700
comments: true
categories: Node
---

I've seen a few patterns lately that misunderstand Node's `require()` API, or simply fail to fully utilize it well.
Let's talk about some examples.

Skipping the File Extension
---

When you `require()` a file in Node, it checks a few possibilities:

* The exact file name
* The file name with `.js` appended
* The file name with `.json` appended

*Note: In case you weren't aware, Node has built-in support for `require`ing JSON files.
No need to manually read the file and call `JSON.parse`.*

This means instead of doing `require('./foo.js')` you can do `require('./foo')`, and Node will figure it out.
I personally feel that it looks cleaner to leave off the file extension, but that's a matter of preference.
There is, however, at least one case I'm aware of where leaving off the file extension improves the flexibility of your code.

Let's say you have a config for your app, stored in `config.json`.
Throughout your app you `require('./config.json')` to load the config.
Now what if you decide the config needs to execute dynamic code (to look up environment variables, for example)?
If you've hardcoded the `.json` in your code, you'll have to find everywhere you've used it and change the extension.

There are two ways to avoid this. The first is to just start with a `.js` file, even if it's a static config.
The second is to just `require('./config')` in your code, so that you can change the file extension without needing to update your code base.

Modules Are Singletons
---

When you load a module/file in Node, you'll always get the same instance.
Consider the following:

```javascript
// a.js
module.exports = Math.Random()
```

```javascript
// main.js
console.log(require('./a'));
console.log(require('./a'));
console.log(require('./a'));
console.log(require('./a'));
```

Intuitively, it seems like main.js would print 4 different random numbers, but it actually prints the same random number 4 times.
This is because Node will only load a file once.
If you `require` the same file multiple times (even from different parts of your code), you'll get the same instance.

I've seen code passing around objects between modules when they could just require the file again and get the same result.
Obviously there are good reasons, like dependency injection, for passing objects around, but sometimes it adds useless complexity.
In cases where you're only passing required objects around "to get the same instance", just require the module where it's needed.

Now the question you should be asking is "what if I *do* want different instances?".
The answer is to export a function, which can then be invoked to get a new instance.

```javascript
// b.js
module.exports = function() {
	return Math.random();
};
```

```javascript
// main.js
var rand = require('./b');
console.log(rand());
console.log(rand());
console.log(rand());
console.log(rand());
```

Don't Load a Module's Submodule
---

This example is obscure. I've only seen it once, but it was a pain.

You can *technically* do something like this: `require('some-module/node_modules/some-submodule')` to load a dependency of a module you depend on, but you shouldn't.

The problem with this approach is that you're inferring a contract with the module's code that isn't present.
The author of that module could easily switch out "some-submodule" for something else, without considering that a breaking change.
Depending on how you're pinning your versions, you may find yourself with an updated module and broken code.

Instead, stop being lazy and install "some-submodule" as a direct dependency.
