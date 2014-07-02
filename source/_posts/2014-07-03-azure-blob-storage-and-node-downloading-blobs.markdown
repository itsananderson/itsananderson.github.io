---
layout: post
title: "Azure Blob Storage and Node: Downloading Blobs"
date: 2014-07-03 16:23:46 -0700
comments: true
categories: Azure Node
---

**This is part of a series on working with Azure Blob Storage in Node.**

1. [Introduction](/blog/2014/06/30/azure-blob-storage-and-node/)
1. [First Steps](/blog/2014/07/01/azure-blob-storage-and-node-first-steps/)
1. [Creating Blobs](/blog/2014/07/02/azure-blob-storage-and-node-creating-blobs/)
1. **Downloading Blobs**
1. [Listing Blobs](/blog/2014/07/07/azure-blob-storage-and-node-listing-blobs/)
1. [Blob Metadata](/blog/2014/07/08/azure-blob-storage-and-node-blob-metadata/)
1. [All Together](/blog/2014/07/09/azure-blob-storage-and-node-all-together/)

Downloading From Blob Storage
---

[Last time](/blog/2014/07/02/azure-blob-storage-and-node-creating-blobs/) we talked about creating Azure blobs.
This time we'll turn things around and talk about downloading Azure blobs.

### Account Connection Boilerplate

As stated in the previous post, to work with blob storage, you first need to create a client instance.
To avoid duplicating code over and over, we'll do that once here, and assume it in the other code samples.

```javascript
var storage = require('azure-storage');
var blobService = storage.createBlobService();
var containerName = 'your-container-name';
blobService.createContainerIfNotExists(
    containerName,
    function(err, result, response) {
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

Again, for the rest of the code samples, we'll assume that they're replacing the "Your code goes here" placeholder in the code above.

### Checking if a Blob Exists

In some cases, you don't need a blob's contents, you just need to know if it exists.
You can do this with the `getBlobProperties` method.

```javascript
var blobName = 'my-nonexistent-blob'
blobService.getBlobProperties(
    containerName,
    blobName,
    function(err, properties, status) {
        if (status.isSuccessful) {
            // Blob exists
        } else {
            // Blob doesn't exist
        }
    });
```

### Downloading a Blob As Text

To download a blob as a text string, use the `getBlobToText` method.

```javascript
var blobName = 'my-awesome-text-blob';
blobService.getBlobToText(
    containerName,
    blobName,
    function(err, blobContent, blob) {
        if (err) {
            console.error("Couldn't download blob %s", blobName);
            console.error(err);
        } else {
            console.log("Sucessfully downloaded blob %s", blobName);
            console.log(blobContent);
        }
    });
```

### Downloading a Blob to a File

To download a blob to a file, use the `getBlobToFile` method.

```javascript
var fs = require('fs');
var fileName = 'hello-world.txt';
var blobName = 'my-awesome-file-blob';
blobService.getBlobToFile(
    containerName,
    blobName,
    fileName,
    function(err, blob) {
        if (err) {
            console.error("Couldn't download blob %s", blobName);
            console.error(err);
        } else {
            console.log("Sucessfully downloaded blob %s to %s", blobName, fileName);
            fs.readFile(fileName, function(err, fileContents) {
                if (err) {
                    console.error("Couldn't read file %s", fileName);
                    console.error(err);
                } else {
                    console.log(fileContents);
                }
            });
        }
    });
```

### Downloading a Blob to a Stream

To download a blob to a stream, use the `getBlobToStream` method.

```javascript
// For simplicity, assume getSomeStream returns a writable stream
var myStream = getSomeStream();
var blobName = 'my-awesome-stream-blob';
blobService.getBlobToStream(
    containerName,
    blobName,
    myStream,
    function(err, blob) {
        if (err) {
            console.error("Couldn't download blob %s", blobName);
            console.error(err);
        } else {
            console.log("Sucessfully downloaded blob %s", blobName);
        }
    });
```

You can also integrate with a web server to enable downloads from blob storage.
This is powerfull when combined with the `getBlobProperties` method.

In the following example, we use the `createReadStream` method so we can easily pipe the stream to the server response object.

```javascript
app.get('/download/:file', function(req, res) {
    var fileName = req.params.file;
    blobService.getBlobProperties(
        containerName,
        fileName,
        function(err, properties, status) {
            if (err) {
                res.send(502, "Error fetching file: %s", err.message);
            } else if (!status.isSuccessful) {
                res.send(404, "The file %s does not exist", fileName);
            } else {
                res.header('Content-Type', properties.contentType);
                blobService.createReadStream(containerName, fileName).pipe(res);
            }
        });
});
```

### Conclusion

In this post we covered how to check whether a blob exists, and how to download the blob.
In the next post, we'll cover [how to list containers and blobs](/blog/2014/07/07/azure-blob-storage-and-node-listing-blobs/).
