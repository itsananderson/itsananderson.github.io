---
layout: post
title: "Using \"Controller As\" Syntax in Angular Routes"
date: 2013-12-03 10:33:58 -0700
comments: true
categories: Angular JavaScript
---

I've been using the new "Controller as" syntax in Angular for a few months, but today I realized I didn't know how to use
the syntax for route controllers. After spending a few minutes scratching my head and "Googling with Bing", I figured it out.
The $routeProvider supports a "controllerAs" property for routes:

```javascript
$routeProvider
    .when('/', {
        templateUrl: 'Views/Home/Index.html',
        controller: 'HomeCtrl',
        controllerAs: 'vm'
    })
    .otherwise({ redirectTo: '/' });
```

That's it. Not very complicated, but hopefully it will save someone a few minutes of research.