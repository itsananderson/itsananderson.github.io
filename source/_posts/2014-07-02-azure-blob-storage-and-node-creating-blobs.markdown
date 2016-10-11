---
layout: post
title: "Azure Blob Storage and Node: Creating Blobs"
date: 2014-07-02 13:27:10 -0700
comments: true
categories: Azure Node
description: In this post, we cover how to create blobs and attach metadata during blob creation.
---


**This is part of a series on working with Azure Blob Storage in Node.**

1. [Introduction](/blog/2014/06/30/azure-blob-storage-and-node/)
1. [First Steps](/blog/2014/07/01/azure-blob-storage-and-node-first-steps/)
1. **Creating Blobs**
1. [Downloading Blobs](/blog/2014/07/03/azure-blob-storage-and-node-downloading-blobs/)
1. [Listing Blobs](/blog/2014/07/07/azure-blob-storage-and-node-listing-blobs/)
1. [Blob Metadata](/blog/2014/07/08/azure-blob-storage-and-node-blob-metadata/)
1. [All Together](/blog/2014/07/09/azure-blob-storage-and-node-all-together/)

Uploading to Azure Blob Storage
---

In the [previous post](/blog/2014/07/01/azure-blob-storage-and-node-first-steps/), we talked about how to set up and connect to an Azure Storage account, and how to create a blob container.
Today we'll discuss how to upload blobs.

### Account Connection Boilerplate

To upload to blob storage, you first need to create a client instance.
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

### Uploading a String

Uploading a string is easy with the `createBlockBlobFromText` method.

```javascript
blobService.createBlockBlobFromText(
    containerName,
    'my-awesome-text-blob',
    'Hello, World!',
    function(error, result, response){
        if(error){
            console.log("Couldn't upload string");
            console.error(error);
        } else {
            console.log('String uploaded successfully');
        }
    });
```

The above code creates a blob called "my-awesome-text-blob" inside our container with the content "Hello, World!".

### Uploading a File

Uploading a file is just as simple as uploading a string. Use the `createBlockBlobFromFile` method.

```javascript
var fileName = 'hello-world.txt';
blobService.createBlockBlobFromFile(
    containerName,
    'my-awesome-file-blob',
    fileName,
    function(error, result, response){
        if(error){
            console.log("Couldn't upload file %s", fileName);
            console.error(error);
        } else {
            console.log('File %s uploaded successfully', fileName);
        }
    });
```

The above code creates a blob called "my-awesome-file-blob" inside our container with the contents of "hello-world.txt".

### Uploading a Stream

Again, uploading streams is easy with azure-storage.
Just use the `createBlockBlobFromStream` method.
The one caveat here is that you need to know the length of the stream you're uploading.

```javascript
// For simplicity, assume getSomeStream() returns a readable stream
var myStream = getSomeStream();
var myStreamLength = getSomeStreamLength();
blobService.createBlockBlobFromStream(
    containerName,
    'my-awesome-stream-blob',
    myStream,
    myStreamLength,
    function(error, result, response){
        if(error){
            console.log("Couldn't upload stream");
            console.error(error);
        } else {
            console.log('Stream uploaded successfully');
        }
    });
```

### Uploading an HTTP Response

You can also download a file and upload it to blob storage.
This is useful for things like caching API responses.

For this, we'll use `createWriteStreamToBlockBlob` to get a writable stream that saves to a blob.

```javascript
var http = require('http');

var fileName = 'index.html';
var domain = 'example.com';
var requestOptions = {
    host: domain,
    port: 80,
    path: '/' + fileName
};

http.get(requestOptions, function (httpResponse) {
    if (200 !== httpResponse.statusCode) {
        console.log('Unexpected status code: %d', httpResponse.statusCode);
    } else {
        var writeStream = blobService.createWriteStreamToBlockBlob(
            containerName,
            fileName,
            {
                contentSettings: {
                    contentType: 'text/html'
                }
            },
            function(error, result, response){
                if(error){
                    console.log("Couldn't upload file %s from %s", fileName, domain);
                    console.error(error);
                } else {
                    console.log('File %s from %s uploaded', fileName, domain);
                }
            });
        httpResponse.pipe(writeStream);
    }
}).on('error', function(e) {
    console.log("Got error: " + e.message);
});
```

This example is a bit more complex, but the main idea is to create a write stream for the blob we want to create.
Then we fetch http://example.com/index.html and `.pipe` the response to the blob's write stream;

If you're doing this in a web server, you'll probably also want to pipe the http.get response to the client.

```javascript
app.get('/somepath', function(req, res) {

    
    http.get(requestOptions, function (httpResponse) {
        if (200 !== httpResponse.statusCode) {
            res.send(502, 'Unexpected status code: ' + httpResponse.statusCode);
        } else {

            // ...
            // Blob stream setup code from above
            // ...

            // Pipe server response to both the blob and the client who made this request
            httpResponse.pipe(writeStream);
            httpResponse.pipe(res);
        }
    }).on('error', function(e) {
        res.send(502, "Got error: " + e.message);
    });
});
```

### Setting a Content Type

You may have noticed that the previoius example passed an extra option to `createWriteStreamToBlockBlob`.
There are a number of properties you can set on a blob, but the most useful is the contentType property.
This determines the Content-Type header that will be set if the blob is accessed or streamed directly from blob storage.

By default the content type is set to 'application/octet-stream'.
If you create a blob with this type and try to access it with your browser, it will simply be downloaded rather than displayed as the desired type (text, html, image).

To set a custom content type, pass something like `{ contentSettings: { contentType: 'image/jpeg' } }` before your callback function.
Note that `createBlockBlobFromFile` will infer the content type from the file extension.

There are a number of other properties that can be set on a blob, including contentEncoding, contentLanguage, and metadata (which will be covered in a later post).

The easiest way to see the different options is to look in [lib\services\blob\blobservice.js](https://github.com/Azure/azure-storage-node/blob/master/lib/services/blob/blobservice.js) inside the azure-storage package.

### Conclusion

In this post we've covered the many ways to create Azure blobs in Node.
Next time we'll talk about [downloading blobs](/blog/2014/07/03/azure-blob-storage-and-node-downloading-blobs/).
