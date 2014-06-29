---
layout: post
title: "Azure Blob Storage and Node"
date: 2014-06-30 14:09:17 -0700
comments: true
categories: Node Azure
---

**This is the introduction to a series on working with Azure Blob Storage in Node.**

1. **Introduction**
1. [First Steps](/blog/2014/07/01/azure-blob-storage-and-node-first-steps/)
1. [Creating Blobs](/blog/2014/07/02/azure-blob-storage-and-node-creating-blobs/)
1. Downloading Blobs
1. Listing Blobs
1. Blob Metadata
1. All Together

Over the weekend I put together a service called [placebacon](http://placebacon.net) that serves placeholder bacon images.
It's certainly not the first placeholder image service, but it's the first one that makes your designs look delicious :)

{% img http://placebacon.net/800/100?image=9 %}

I decided to build the service on Azure so that it can easily be scaled if anyone actually starts using it.
Caching generated images with Azure Blob Storage made the most sense.

The Azure team has released an npm package called [azure-storage](https://www.npmjs.org/package/azure-storage), which I used to interact with the Azure Blobs.
The documentation for the azure-storage package is fairly good, but I still mostly found myself digging into the source code to figure things out.
I'm writing this blog series to make it easier for other Node developers to work with the Node bindings for Azure Blob Storage.

Placebacon's Service Architecture
---

To set a framework for the upcoming discussions about Blob Storage, I'm providing a high-level overview of placebacon's architecture.

First, I need a way to resize images in Node.
After spending several frustrating hours trying to find an image manipulation module that works in Windows, I decided that a Linux VM would be the easiest way to resize images in Node.
Since I'm using the Azure infrastructure, setting up a Linux VM is straightforward, but I also want to take advantage of the simple scaling ability of Azure Websites.

To accomplish this, I decided to have a lightweight Ubuntu VM for resizing images.
Then I put together an Azure website (in Node, of course) that requests resized images from the service running on Ubuntu, and caches the results in Blob Storage.

This (literal) back-of-an-envolope drawing illustrates the layout.

{% img /images/post-images/placebacon-architecture.jpg %}

With the architecture design out of the way, next time I'll cover the [steps for getting started with Azure Blob Storage](/blog/2014/07/01/azure-blob-storage-and-node-first-steps/).
