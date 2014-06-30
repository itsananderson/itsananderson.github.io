---
layout: post
title: "Azure Blob Storage and Node: Listing Blobs"
date: 2014-07-07 17:33:03 -0700
comments: true
categories: Azure Node
---

**This is part of a series on working with Azure Blob Storage in Node.**

1. [Introduction](/blog/2014/06/30/azure-blob-storage-and-node/)
1. [First Steps](/blog/2014/07/01/azure-blob-storage-and-node-first-steps/)
1. [Creating Blobs](/blog/2014/07/02/azure-blob-storage-and-node-creating-blobs/)
1. [Downloading Blobs](/blog/2014/07/03/azure-blob-storage-and-node-downloading-blobs/)
1. **Listing Blobs**
1. Blob Metadata
1. All Together

Listing Containers and Blobs in Storage
---

[Last time](/blog/2014/07/03/azure-blob-storage-and-node-downloading-blobs/) we talked about downloading blobs from storage.
This time we'll cover listing your existing containers and blobs.

### Listing Containers in an Account

Listing the containers in a storage account is easy with the `listContainerSegmented` method.

```javascript
var storage = require('azure-storage');
var blobService = storage.createBlobService();
blobService.listContainersSegmented(null, function(err, result) {
    if (err) {
        console.log("Couldn't list containers");
        console.error(err);
    } else {
        console.log('Successfully listed containers');
        console.log(result.entries);
        console.log(result.continuationToken);
    }
});
```

If you have enough containers, not all of them will be returned in one call.
If `result.continuationToken` is not null, there are more entries.
You can get the next segment of entries by calling listContainersSegmented again with `result.continuationToken` as the first result.

If you want to aggregate all containers, you can use the following code.

```javascript
var storage = require('azure-storage');
var blobService = storage.createBlobService();

var containers = [];
function aggregateContainers(err, result, cb) {
    if (err) {
        cb(er);
    } else {
        containers = containers.concat(result.entries);
        if (result.continuationToken !== null) {
            blobService
                .listContainersSegmented(result.continuationToken, aggregateContainers);
        } else {
            cb(null, containers);
        }
    }
}

blobService.listContainersSegmented(null, function(err, result) {
    aggregateContainers(err, result, function(err, containers) {
        if (err) {
            console.log("Couldn't list containers");
            console.error(err);
        } else {
            console.log(containers);
        }
    });
});
```

### Listing Blobs in a Container

You can list blobs in a container with the `listBlobsSegmented` method.

```javascript
var storage = require('azure-storage');
var blobService = storage.createBlobService();
var containerName = 'your-container-name';
blobService.listBlobsSegmented(containerName, null, function(err, result) {
    if (err) {
        console.log("Couldn't list blobs for container %s", containerName);
        console.error(err);
    } else {
        console.log('Successfully listed blobs for container %s', containerName);
        console.log(result.entries);
        console.log(result.continuationToken);
    }
});
```

As with containers, you can use the continuationToken to aggregate results.

```javascript
var storage = require('azure-storage');
var blobService = storage.createBlobService();
var containerName = 'your-container-name';

var blobs = [];
function aggregateBlobs(err, result, cb) {
    if (err) {
        cb(er);
    } else {
        blobs = blobs.concat(result.entries);
        if (result.continuationToken !== null) {
            blobService.listBlobsSegmented(
                containerName,
                result.continuationToken,
                aggregateBlobs);
        } else {
            cb(null, blobs);
        }
    }
}

blobService.listBlobsSegmented(containerName, null, function(err, result) {
    aggregateBlobs(err, result, function(err, blobs) {
        if (err) {
            console.log("Couldn't list blobs");
            console.error(err);
        } else {
            console.log(blobs);
        }
    });
});
```

### Listing Blobs and Containers by Prefix

If you want to "query" blobs and containers by a prefix, you can do so with the `listContainersSegmentedWithPrefix` and `listBlobsSegmentedWithPrefix` methods.

```javascript
var storage = require('azure-storage');
var blobService = storage.createBlobService();
var prefix = 'images-';
blobService.listContainersSegmentedWithPrefix(
    prefix,
    null,
    function(err, result) {
        if (err) {
            console.log("Couldn't list containers");
            console.error(err);
        } else {
            console.log("Found containers with prefix %s", prefix);
            console.log(result.entries);
        }
    });
```

```javascript
var storage = require('azure-storage');
var blobService = storage.createBlobService();
var containerName = 'your-container-name';
var prefix = 'image-';
blobService.listBlobsSegmentedWithPrefix(
    containerName,
    prefix,
    null,
    function(err, result) {
        if (err) {
            console.log("Couldn't list blobs");
            console.error(err);
        } else {
            console.log("Found blobs with prefix %s", prefix);
            console.log(result.entries);
        }
    });
```

### Conclusion

In this post we covered listing blobs and containers in your storage account.
In the next post, we'll cover blob metadata.
