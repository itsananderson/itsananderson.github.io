---
layout: post
title: "Only Run One Suite or Spec in Jasmine"
date: 2014-09-25 00:30:43 -0700
comments: true
categories: JavaScript Jasmine TDD
---

My team uses Karma (and thus Jasmine) to test our AngularJS applications.
Sometimes when a single test is failing, it's a pain to re-run every test while you iterate on a fix.

Turns out there's a way to only run a single suite of tests, or even a single test.

Suppose you have the following tests:

```javascript
describe(“my feature a”, function() {
    it(“has a foo”, function() {
        // ...
    });

    it(“has a bar”, function() {
        // ...
    });
});

describe(“my feature b”, function() {
    it(“has a foo”, function() {
        // ...
    });

    it(“has a bar”, function() {
        // ...
    });
});
```

If you have tests failing in "my feature a", you can just run those tests by changing the `describe` call to a `ddescribe` call.

```javascript
// Only this suite will run
ddescribe(“my feature a”, function() {
    it(“has a foo”, function() {
        // ...
    });

    it(“has a bar”, function() {
        // ...
    });
});

describe(“my feature b”, function() {
    it(“has a foo”, function() {
        // ...
    });

    it(“has a bar”, function() {
        // ...
    });
});
```

If you've narrowed down to a single spec, you can re-run that single spec by changing `it` to `iit`.

```javascript
describe(“my feature a”, function() {
    // Only this spec will run
    iit(“has a foo”, function() {
        // ...
    });

    it(“has a bar”, function() {
        // ...
    });
});

describe(“my feature b”, function() {
    it(“has a foo”, function() {
        // ...
    });

    it(“has a bar”, function() {
        // ...
    });
});
```

If you want to run multiple specs, you do that by simply changing each to `iit`.

```javascript
describe(“my feature a”, function() {
    // This spec will run
    iit(“has a foo”, function() {
        // ...
    });

    it(“has a bar”, function() {
        // ...
    });
});

describe(“my feature b”, function() {
    // This spec will run
    iit(“has a foo”, function() {
        // ...
    });

    it(“has a bar”, function() {
        // ...
    });
});
```

This was a pretty useful discovery for me. Hopefully it is for you as well.
