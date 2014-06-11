---
layout: post
title: "Creating an Angular Markdown Preview"
date: 2013-10-09 17:56:26 -0700
comments: true
categories: Angular JavaScript Markdown
---

I've been exploring [Angular](http://angularjs.org/ "Angular Project Homepage") over the last few months, and I really like the way it works.

Because I like to use [Markdown](http://en.wikipedia.org/wiki/Markdown) for things like my dev journal, I have been wanting to implement my own Markdown preview tool that I could customize to work just the way I want. When I first started learning Angular, I decided to see how easy it would be to write an Angular Markdown preview tool using Angular. Turns out it's super simple.

If you'd rather skip straight to the demo, you can [view the finished markdown preview](http://itsananderson.azurewebsites.net/markdown).

I won't go into great detail about the purpose of Angular, since it's readily available elsewhere. In this project, I just wanted a simple way to bind to a textbox so that I could update an HTML Markdown preview. Angular's declarative binding makes it an excellent choice for this.

### Rendering an Angular Markdown Preview

Since I didn't want to write my own markdown converter, I decided to look for an existing one. I quickly settled on this [markdown npm package](https://npmjs.org/package/markdown). I installed the package, then moved the converter code into my 'lib/js' folder so that it would be accessible from my web app. 

To make the front-end as simple as possible, I decided to [create an Angular filter](http://docs.angularjs.org/guide/dev_guide.templates.filters.creating_filters) to provide the conversion from Markdown to HTML.

The code is pretty simple. The markdown npm package adds a "markdown" object to the "window" global, so it's easy to access the converter.

```javascript
var app = angular.module('app', [])

	.filter('markdown', function() {
		return function(text) {
			if (typeof text == "undefined") {
				return "";
			}
			return markdown.toHTML(String(text));
		}
	});
```

**Update** 4/25/14: In newer versions of Angular you'll need to include `angular-sanitize.js` on the page, and bind with the `ng-bind-html` attribute. Props to [Dick](https://twitter.com/michaeldick) and [Julien](http://konpa.fr) for pointing this out.
