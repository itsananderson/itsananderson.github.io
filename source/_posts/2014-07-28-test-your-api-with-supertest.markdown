---
layout: post
title: "How To Test Your API With Supertest"
date: 2014-07-28 14:18:23 -0700
comments: true
categories: Testing Node
---

[Supertest](https://npmjs.org/package/supertest) is a fantastic tool for testing your API.
Here's how I use it to test APIs.

Setup
---

You can configure supertest in a couple ways.
If you're testing an external site, you can configure supertest with the site's base url:

```javascript
var request = require('supertest')('http://example.com/');
```

If you're testing an Express app, you can pass the app to supertest, and let it worry about setup/teardown of your site.

```javascript
var myApp = require('../app.js');
var request = require('supertest')(myApp);
```

The great thing about this approach is that supertest takes care of starting up and shutting down your site on a random port.
This allows you to just write your tests, without worrying about starting apps, conflicting ports, etc.

Writing a Test
---

Once you've imported supertest, it's quite simple to use.
In my examples, I'll be using the test framework provided by [Mocha](https://npmjs.org/package/mocha).

```javascript
describe('my api', function() {
    it('returns hello world', function(done) {
        request.
            .get('/')
            .expect('Hello, World!', done);
    });
});
```

This is about as simple as the tests get.
We call the root of the site, and expect the response body to equal "Hello, World!".

Since the request is async, we take in a "done" parameter (which Mocha passes to us).
We call that at the end of the test when we've actually had a chance to verify the server response.

You can easily test a JSON response with supertest:

```javascript
request.
    .get('/')
    .expect({message: "Hello, World!"}, done);
```

Headers are also simple to verify.

```javascript
request.
    .get('/foo')
    .expect('Content-Type', 'application/json', done);
```

Status codes are even easier.

```javascript
request
    .get('/some-error-route')
    .expect(500, 'Oops. Something went wrong', done);
```

You can do non-GET requests.

```javascript
var user = {name: 'Bob'};
request
    .post('/create-user')
    .send(user)
    .expect({success:true}, done);
```

You can set headers for the request.

```javascript
request
    .get('/foo')
    .set('Accept', 'text/plain')
    .expect('Content-Type', 'text/plain', done);
```
You can match regular expressions against headers and bodies.

```javascript
request
    .get('/redirect')
    .expect('Location': /\/destination/)
    .expect(301, done);
```

If you need to do multiple requests in series, you can chain the "expect" callbacks:

```javascript
// Kick things off
purgeUsers();

function purgeUsers() {
    request
        .get('/users/purge')
        .expect({success:true}, checkNoUsers);
}

function checkNoUsers(err) {
    if (err) done(err);
    request
        .get('/users')
        .expect([], addUser);
}

function addUser() {
    if (err) done(err);
    request
        .post('/users')
        .send({name: 'Bob'})
        .expect({success:true}, checkUsers);
}

function checkUsers(err) {
    if (err) done(err);
    request
        .get('/users')
        .expect([{name: 'Bob'}], done);
}
```

Finally, if you need to do some custom processing on a result, use the `.end()` function.

```javascript
var assert = require('assert');
request
    .get('/foo')
    .end(function(err, result) {
        assert.equal(result.body.foo, 'Bar');
        done();
    });
```

There are probably more features that I'm not aware of, but this is a rundown of the ones I'm most familiar with.
