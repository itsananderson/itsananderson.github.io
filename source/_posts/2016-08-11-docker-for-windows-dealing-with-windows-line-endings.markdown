---
layout: post
title: "Docker for Windows: Dealing With Windows Line Endings"
date: 2016-08-11 23:54:08 -0700
comments: true
categories: Docker
---

One of the issues with Docker (or any Linux/macOS based system) on Windows is the difference in how line endings are handled.
Windows ends lines in a carriage return and a linefeed `\r\n` while Linux and macOS only use a linefeed `\n`.
This becomes a problem when you try to create a file in Windows and run it on a Linux/macOS system, because those systems treat the `\r` as a piece of text rather than a newline.

As a concrete example, if you try to clone the [official WordPress docker image](https://github.com/docker-library/wordpress) and build the image on Windows, you'll run into problems when it tries to execute the `docker-entrypoint.sh` file.

The first line of that file is `#!/bin/bash`, which is the [shebang](https://en.wikipedia.org/wiki/Shebang_(Unix&#41;) syntax to say "run this file using /bin/bash"

However, if the file originated in windows, that first line will be interpreted as "run this file using /bin/bash\r", and "bash\r" of course doesn't exist, so you get this error:

```
$ docker run wordpress
standard_init_linux.go:175: exec user process caused "no such file or directory"
```

There are a couple of ways to handle this issue.

### Converting Line Endings During Build

Unix has a handy CLI tool for converting line endings called `dos2unix`.
If you want to create a robust image, you can install do2unix as a dependency, then convert any files that you copy into the image.
Then as a cleanup step, you can uninstall do2unix from the image (unless the image depends on it after the build).

Your Dockerfile might look something like this:

```
FROM ubuntu:latest

RUN apt-get update && apt-get install -y dos2unix

COPY docker-entrypoint.sh /entrypoint.sh

# RUN dos2unix /entrypoint.sh && apt-get --purge remove -y dos2unix && rm -rf /var/lib/apt/lists/*

ENTRYPOINT ["/entrypoint.sh"]
```

The main idea is to copy the script onto the machine, then use dos2unix to convert the line endings, and finally remove dos2unix from the machine (and clean up the files created by apt-get).

This is a good option if you're managing the image, but what if you're trying to build an image that someone else maintains?

### Cloning Git Projects With Unix Line Endings

If you just want to clone and build an existing Docker image, you can use a Git flag to store the repository locally with Unix style line endings.

```
git clone git@github.com:docker-library/wordpress.git --config core.autocrlf=input
```

However, it's worth noting that any new files you create will likely have Windows line endings, so you'll still need to convert them before using them inside the Docker image.

### Conclusion

That should cover the basics of line endings between Windows and Linux/macOS.
These techniques apply beyond just Docker, but hopefully the Docker-specific details will help someone who's struggling with Docker for Windows.
