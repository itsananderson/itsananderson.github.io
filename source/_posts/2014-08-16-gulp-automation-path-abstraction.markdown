---
layout: post
title: "Gulp Automation: Path Abstraction"
date: 2014-08-16 00:57:44 -0700
comments: true
categories: Gulp
---

If you use Gulp for anything more than a trivial project, you'll find that you reference paths *a lot*.
Paths for sources, paths for destinations.
Paths to watch for changes.
You get the picture.

Without some organization, things can quickly get out of hand.

Store Paths In Variables
---

As tempting as it might be to shove an extra path into a task definition, resist the urge.
Instead, define your paths as variables.
This allows you to share and compose path groups without repetition.

As an example, consider a common use case.
You want to lint your JavaScript for your application code and tests, but you only want to minify your application code and run tests against your test code.
You could approach it like this:

```javascript
gulp.task('lint', function() {
    gulp.src(['app/*.js', 'test/*.js'])
        .pipe(lint());
});

gulp.task('minify', function() {
    gulp.src(['app/*.js'])
        .pipe(lint());
});

gulp.task('test', function() {
    gulp.src(['test/*.js'])
        .pipe(lint());
});
```

This isn't bad, but things can quickly get out of hand.
At this point already, if you moved your application code to a folder called 'src', you'd have to update your gulpfile in two places.
Three if you'd added a 'watch' task.

When you abstract your paths, you can reuse them throughout your gulpfile.

```javascript
var appPaths = ['app/*.js'];
var testPaths = ['test/*.js'];

gulp.task('lint', function() {
    gulp.src(appPaths.concat(testPaths))
        .pipe(lint());
});

gulp.task('minify', function() {
    gulp.src(appPaths)
        .pipe(minify());
});

gulp.task('test', function() {
    gulp.src(testPaths)
        .pipe(test());
});
```

Again, this is a simple example, but hopefully it illustrates the point.

Breaking Into `require`able Pieces
---

Once your paths are in variables, you can abstract them further by putting them their own file.

To illustrate, suppose you have Karma tests.
Karma uses its own configuration file, but gulp-karma overrides some if them, which can make it hard to avoid duplicating configurations.
Consider the following config:

```javascript
// karma.conf.js
module.exports = function (config) {
    config.set({
        files: [
            'vendor/angular.js',
            'app/*.js',
            'test/*.js'
        ],
        reporters: ['spec'],
        autoWatch: true,
        frameworks: ['jasmine'],
        browsers: ['PhantomJS'],
        plugins: [
            'karma-jasmine',
            'karma-spec-reporter',
            'karma-phantomjs-launcher'
        ],
    });
};
```

Not too bad, but again, we're duplicating logic.
Back in the gulpfile, we'll have a task like this:

```javascript
var vendorPaths = ['vendor/angular.js'];

gulp.task('karma', function() {
    gulp.src(vendorPaths.concat(appPaths).concat(testPaths))
        .pipe(karma({ configFile: 'karma.conf.js' }));
});
```

Suddenly, there's path duplication between the Karma config file and the gulpfile.
To fix this, simply define your paths in another file that's included by both.

```javascript
var app = ['app/*.js'];
var test = ['test/*.js'];
var vendor = ['vendor/angular.js'];
module.exports = {
    app: app,
    test: test,
    vendor: vendor,
    karma: vendor.concat(app).concat(test)
};
```

Then your gulpfile should look like this:


```javascript
var paths = require('./paths');

gulp.task('lint', function() {
    gulp.src(paths.app.concat(paths.test))
        .pipe(lint());
});

gulp.task('minify', function() {
    gulp.src(paths.app)
        .pipe(minify());
});

gulp.task('test', function() {
    gulp.src(paths.test)
        .pipe(test());
});

gulp.task('karma', function() {
    gulp.src(paths.karma)
        .pipe(karma({ configFile: 'karma.conf.js' }));
});
```

And your karma.conf.js can look like this:

```javascript
// karma.conf.js
var paths = require('./paths');

module.exports = function (config) {
    config.set({
        files: paths.karma,
        reporters: ['spec'],
        autoWatch: true,
        frameworks: ['jasmine'],
        browsers: ['PhantomJS'],
        plugins: [
            'karma-jasmine',
            'karma-spec-reporter',
            'karma-phantomjs-launcher'
        ],
    });
};
```

There you have it.
Nice and DRY.

Here's a [Gist of the completed example](https://gist.github.com/itsananderson/61bad88e4470679a4f13).
