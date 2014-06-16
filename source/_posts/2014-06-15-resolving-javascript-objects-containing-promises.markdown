---
layout: post
title: "Resolving JavaScript Objects Containing Promises"
date: 2014-06-15 15:12:43 -0700
comments: true
categories: JavaScript Promises
---

Dealing with multiple asynchronous calls makes JavaScript promises outshine the classic callback pattern.
Unlike callbacks, you can wait for multiple promises to complete before proceeding.
For example, in the [Q](https://github.com/kriskowal/q) framework, you can use the `Q.all()` function to wait for multiple promises to complete.

```javascript
function makePromise(val) {
  var deferred = Q.defer();
  deferred.resolve(val);
  return deferred.promise;
}

var promises = [
  makePromise(1),
  makePromise(2),
  makePromise(3),
  makePromise(4),
];

Q.all(promises, function(results) {
  console.log(results); // [1,2,3,4]
});
```

Now, this works well for an array, but as promise dependencies increase, it may be useful to reference these values more descriptively than an array index.
Usually, you'd want to wrap dependencies in an object like this:

```javascript
var dependencies = {
  'db': connectToDb(),
  'api': getApi(),
  'third': someOtherDependency()
}
```

Now, Q doesn't have a way to await the promises inside this object.
If you just pass it to `Q.all()` it will finish before the promises are resolved, because the "dependencies" variable is an object, not a promise.
However, there is a pretty simple workaround using Lo-Dash (or Underscore):

```
Q.all(_.values(dependencies)).then(function(results) {
  return _.zipObject(_.keys(dependencies), results);
}).then(function(deps) {
  console.log(deps);
  // {
  //   "db": [db connection],
  //   "api" [api object],
  //   "third": [third dependency]
  // }
});
```

Basically we're unzipping the keys and values from the "dependencies" object, then rezipping them with the `_.zipObject()` function when the promises are resolved.

Note that you can safely mix promises and non-promises.
For example:

```javascript
var dependencies = {
  'db': connectToDb(),
  'name': 'Bob',
  'version': 3
}
```

Also note that this only works on a "flat" object.
Any nested objects won't be resolved.
It wouldn't be terribly difficult to write a utility function for recursively traversing an object and awaiting its promises, but I'll leave that as an exercise for the reader.
