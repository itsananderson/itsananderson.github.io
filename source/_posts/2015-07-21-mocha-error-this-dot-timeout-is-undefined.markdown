---
layout: post
title: "Mocha Error - this.timeout is undefined"
date: 2015-07-21 16:25:25 -0700
comments: true
categories:
- Mocha
- Node
---

If you're using an ES6 compiler like TypeScript or Babel, you may have run into an odd error when you tried to call `this.timeout()` from your Mocha tests.


```javascript
it("foo", (done) => {
	this.timeout(1000);
	// test some things
});
```

If you look at the compiled output, the source of the problem becomes evident.
The compiler is taking the value of `this` from outside the test.
This is also the behavior you'd see if you used a JS engine with ES6 support.

```javascript
var _this = this;
it("foo", function(done) {
	_this.timeout(1000);
	// test some things
});
```

Arrow functions specify that the scope of the "this" variable inside the function is the same as its scope outside the function.
Unfortunately, in this case, that isn't what we want. We want "this" to be the Mocha object that we can call `this.timeout()` on.

Switching back to the old-school function style fixes the problem:

```javascript
it("foo", function(done) {
	this.timeout(1000);
	// test some things
});
```

And there you have it. Be careful with "arrow" functions in Mocha tests. They're fine to use in most cases, but if you need to call `this.timeout()`, make sure you switch back to the old-school function syntax.
