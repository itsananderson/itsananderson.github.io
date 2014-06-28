---
layout: post
title: Thoughts On Implementing a REST Level 3 API in Node
date: 2014-06-27 20:25:43 -0700
comments: true
categories: REST Node 
---

**Warning: stream of consciousness ahead**

I've been thinking about the the pieces that would be needed to implement a REST Level 3 API in Node.
I won't go into the details of REST Level 3 in this post, but the one of the most important concepts is that clients to the API don't manually build URIs for their API calls, but instead receive them as hypermedia metadata from other calls to your API.

Node has some great frameworks for building web servers.
I frequently use [Express](http://expressjs.com/), which is built on top of the [Connect](http://www.senchalabs.org/connect/) middleware package.
Unfortunately, Express doesn't make it easy to add hypermedia links to API responses in a DRY way.

Another option is [node-restify](http://mcavage.me/node-restify/).
This is a popular choice for building REST APIs in Node, but again, it falls down when it comes to Hypermedia.
Here at least you can link to different API endpoints without hardcoding URLs, but there's still no unified way to automatically add hypermedia links to responses.

I've tried other frameworks as well.
[Hapi](http://hapijs.com/) comes closest, but still doesn't quite solve my problems with hypermedia.

Perhaps I'm spoiled by the relatively elegant solutions that can be accomplished in strictly typed languages.
In C#, for example, you can keep a registry of hypermedia providers associated with different types.
When a response of a specific type is returned by a controller, the assocated hypermedia provider is invoked, and it returns a dictionary of URIs for that type.
The controller doesn't even have to be aware of the hypermedia provider.

In JavaScript, it's a little harder to associate types with providers, since most responses are just anonymous objects.
I do have some alternative ideas, though.
The first that comes to mind is to associate with URLs.
Most importantly, being able to say "this route, and any route below it uses the same provider".
For example, consider the following routes.

```
GET  /users
POST /users
GET  /users/1
GET  /users/1/edit
GET  /users/1/delete
```

All of those URLs could probably use an identical (or almost identical) link provider.
Something like the following would be useful:

```javascript
app.hypermedia('/users*', function(route, user) {
    if ( user ) {
        return {
            "self": "/users/" + user.id,
            "edit": "/users/" + user.id + "/edit",
            "delete": "/users/" + user.id + "/delete",
        };
    } else {
        return {
            "find-all": "/users",
            "create": "/users",
        };
    }
});
```

I'm not really tied to that syntax, nor is this an awesome example.
The main idea is, I want to abstract hypermedia so controllers don't have to worry about it.

The other part of this is that controllers need to send their response back through a middleware, rather than directly to the client.
In my mind, this means the best solution is to return the object.
Since Node is async, simply returning the object probably isn't an option in most cases.
This means the next best choice would be to return a promise from the controller.
The middleware then waits for the promise to resolve, and then handles the result.
Finally, the controller should still be able to send a response directly to the client through the res.send() function.
In that case, no hypermedia links would be provided.

If the controller needs to make an async call, but that call doesn't automatically provide a promise, a nice simplification would be to provide a function on the response object that does the same thing.
For example:

```javascript
app.get('/user/:id', function(req, res) {
    db.getUser(req.params.id, function(err, user) {
        res.respond(user); // res.respond could invoke hypermedia providers before response 
    });
});
```

Of course you can usually use Q's "denodify" utilities, but the point here is a little flexibility. Here's how a Q promise might be returned. The framework would "then" the promise and essentially forward the result to `req.respond()`.

```javascript
app.get('/user/:id', function(req, res) {
    return Q.nfinvoke(db, "getUser", req.params.id);
});
```

If you want to do something with the promise result, you still can with promises.

```javascript
app.get('/user/:id', function(req, res) {
    return Q.nfinvoke(db, "getUser", req.params.id)
        .then(function(user) {
            var err = new Error('User not found');
            err.status = 404;
            throw err;
        });
});
```

The other great thing to note here is that we don't have to call `next(err)` to pass this exception to our error handler. We can throw it, and let the framework `.catch()` the exception from the promise we return.

The basic idea for an implementation of this is that the routing framework calls your endpoint handler, and expects it to do one of three things:

1. Return a promise, or value (buffer, stream, string, javascript object)
1. Call `next()` to pass controll to the next handler
1. Call `res.send()`, `res.write()`, or `res.end()` like you would in Express

In the first case, the framework can allow hypermedia providers, and theoretically anything, to modify the response "on the way out the door".
This would be my main objective in implementing something like this.

I may take a stab at a prototype, but I realize that at this point, any Node web framework probably needs a name that starts with "Yet Another".
Mostly, I'm curious how well it would work for "real" api development.

Thoughts? Does something like this already exist? Hit me up on [my Twitter account](https://twitter.com/itsananderson/).
