---
layout: post
title: "React: this.state is undefined"
date: 2015-05-07 23:42:13 -0700
comments: true
categories: React
---

I ran into this today, so I'm writing it down in case it helps someone else.

Running something like the following, I found that `this.state` was undefined during render.

```javascript
class MyComponent extends Component {
	getInitialState() {
		return { count: 0 };
	}

	render() {
		return <div>{this.state.count}</div>; // Error: this.state is undefined
	}
}
```

After a little searching, I found the [release notes for React 0.13.0-beta-1](https://facebook.github.io/react/blog/2015/01/27/react-v0.13.0-beta-1.html#es6-classesa), where they explained that ES6 classes should set initial state in the constructor, rather than defining a `getInitialState` method.

```javascript
class MyComponent extends Component {
	constructor() {
		this.state = { count: 0 };
	}

	render() {
		return <div>{this.state.count}</div>; // Yay, it works!
	}
}
```

Pretty simple, but not immediately obvious if you don't know about it.
