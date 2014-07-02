---
layout: post
title: "Azure Blob Storage and Node: Blob Metadata"
date: 2014-07-08 20:47:12 -0700
comments: true
categories: Azure Node
---

**This is part of a series on working with Azure Blob Storage in Node.**

1. [Introduction](/blog/2014/06/30/azure-blob-storage-and-node/)
1. [First Steps](/blog/2014/07/01/azure-blob-storage-and-node-first-steps/)
1. [Creating Blobs](/blog/2014/07/02/azure-blob-storage-and-node-creating-blobs/)
1. [Downloading Blobs](/blog/2014/07/03/azure-blob-storage-and-node-downloading-blobs/)
1. [Listing Blobs](/blog/2014/07/07/azure-blob-storage-and-node-listing-blobs/)
1. **Blob Metadata**
1. [All Together](/blog/2014/07/09/azure-blob-storage-and-node-all-together/)


Working With Azure Blob Metadata
---

In the [previous post](/blog/2014/07/07/azure-blob-storage-and-node-listing-blobs/), we talked about how to list blobs and containers in your account.
Today we'll discuss how to work with blob properties and metadata.

### Account Connection Boilerplate

To work with blob storage, you first need to create a client instance.
To avoid duplicating code over and over, we'll do that once here, and assume it in the other code samples.

```javascript
var storage = require('azure-storage');
var blobService = storage.createBlobService();
var containerName = 'your-container-name';
blobService.createContainerIfNotExists(containerName, function(err, result, response) {
    if (err) {
        console.log("Couldn't create container %s", containerName);
        console.error(err);
    } else {
        if (result) {
            console.log('Container %s created', containerName);
        } else {
            console.log('Container %s already exists', containerName);
        }

        // Your code goes here
    }
});
```

Now for the rest of the code samples, we'll assume that they're replacing the "Your code goes here" placeholder in the code above.

### Fetching Blob Properties

You can fetch the properties for a blob with the `getBlobProperties` method.

```javascript
var blobName = 'my-awesome-blob';
blobService.getBlobProperties(containerName, blobName, function(err, result, response) {
    if (err) {
        console.error("Couldn't fetch properties for blob %s", blobName);
        console.error(err);
    } else if (!response.isSuccessful) {
        console.error("Blob %s wasn't found container %s", blobName, containerName);
    } else {
        console.log("Successfully fetched properties for blob %s", blobName);
        console.log(result);
    }
});
```

The blob properties object includes the blob's metadata.
If you only need the metadata, you can request just that with the next snippet.

### Fetching Blob Metadata

You can fetch the properties for a blob with the `getBlobProperties` method.

```javascript
var blobName = 'my-awesome-blob';
blobService.getBlobMetadata(containerName, blobName, function(err, result, response) {
    if (err) {
        console.error("Couldn't fetch metadata for blob %s", blobName);
        console.error(err);
    } else if (!response.isSuccessful) {
        console.error("Blob %s wasn't found container %s", blobName, containerName);
    } else {
        console.log("Successfully fetched metadata for blob %s", blobName);
        console.log(result.metadata);
    }
});
```

### Updating Blob Properties and Metadata

A blob's properties and metadata can be updated with the `setBlobProperties` and `setBlobMetadata` methods.

```javascript
// Error checking removed for simplicity
var blobName = 'my-awesome-blob';
blobService.getBlobProperties(
    containerName,
    blobName,
    function(err, result, response) {
        var properties = result;
        properties.contentType = 'image/jpeg';
        blobService.setBlobProperties(
            containerName,
            blobName,
            properties,
            function(err, result, response) {
                console.log('Successfully updated properties for blob %s', blobName);
            });
    });
```

```javascript
// Error checking removed for simplicity
var blobName = 'my-awesome-blob';
blobService.getBlobMetadata(
    containerName,
    blobName,
    function(err, result, response) {
        var metadata = result.metadata;
        metadata.hitcount = parseInt(metadata.hitcount || 0) + 1;
        blobService.setBlobMetadata(
            containerName,
            blobName,
            metadata,
            function(err, result, response) {
                console.log('Successfully updated metadata for blob %s', blobName);
            });
    });
```

### Avoiding Race Conditions

In the previous examples, there's a potential race condition.
If the blob is updated between the time you fetch the properties/metadata and the time when you update them, the changes will override each other.

You can avoid this problem by using "leases".

The idea of a blob lease is that it lets you temporarily "lock" a blob for modifications.
Once a lease is acquired for a blob, any modifications must include that lease's Id.

By default, leases don't have a set duration.
This means they won't expire automatically, and will need to be manually released.

The other alternative is to set a specific lease duration.
This is a good failsafe.
If your script throws an error and never releases the lease, it will still eventually expire.

Valid lease durations are between 15 and 60 seconds.
If you need a longer lease, you can renew the lease before it expires.

```javascript
// Recursively attempts to acquire a lease
function acquireLease(containerName, blobName, cb) {
    blobService.acquireLease(
        containerName,
        blobName,
        { leaseDuration: 15 },
        function(err, result) {
            if (err) {
                if (err.code === 'LeaseAlreadyPresent') {
                    setTimeout(function() {
                        acquireLease(containerName, blobName, cb);
                    }, 100);
                } else {
                    cb(err);
                }
            } else {
                cb(null, result.id);
            }
        });
}


var blobName = 'my-awesome-blob';
acquireLease(containerName, blobName, function(err, leaseId) {
    if (err) {
        console.error("Couldn't acquire lease on blob %s", blobName);
        console.error(err);
    } else {
        blobService.getBlobMetadata(
            containerName,
            blobName,
            { leaseId: leaseId },
            function(err, blob, response) {
                if (err) {
                    console.error("Couldn't get metadata for blob %s", blobName);
                    console.error(err);
                } else {
                    var metadata = blob.metadata;
                    metadata.hitcount = parseInt(metadata.hitcount || 0) + 1;
                    blobService.setBlobMetadata(
                        containerName,
                        blobName,
                        metadata,
                        function(err, blob, response) {
                            console.log("Blob %s metadata updated", blobName);
                            blobService.releaseLease(
                                containerName,
                                blobName,
                                leaseId,
                                function() {});
                        });
                }
            });
    }
});
```

### Conclusion

In this post we covered interacting with blob properties and metadata.
In the final post in this series, we'll talk about putting everything together to create an blob-backed web service.
