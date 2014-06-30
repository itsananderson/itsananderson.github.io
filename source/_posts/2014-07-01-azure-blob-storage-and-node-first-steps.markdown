---
layout: post
title: "Azure Blob Storage and Node: First Steps"
date: 2014-07-01 06:00:03 -0700
comments: true
categories: Azure Node
---

**This is part of a series on working with Azure Blob Storage in Node.**

1. [Introduction](/blog/2014/06/30/azure-blob-storage-and-node/)
1. **First Steps**
1. [Creating Blobs](/blog/2014/07/02/azure-blob-storage-and-node-creating-blobs/)
1. [Downloading Blobs](/blog/2014/07/03/azure-blob-storage-and-node-downloading-blobs/)
1. [Listing Blobs](/blog/2014/07/07/azure-blob-storage-and-node-listing-blobs/)
1. [Blob Metadata](/blog/2014/07/08/azure-blob-storage-and-node-blob-metadata/)
1. All Together

Getting Started With Azure Blob Storage and Node
---

The Azure team has made it really simple to start using Blob Storage in Node.
The first thing you'll want to do is install the [azure-storage](https://www.npmjs.org/package/azure-storage) module into your project.

```bash
# Make sure you have a package.json so npm can save your azure-storage dependency 
npm install --save azure-storage
```

### Create Storage Account

Unless you already have one, you'll need to create an Azure storage account.
They're pretty simple to create.
From the [Azure portal](https://manage.windowsazure.com/), click "New" in the bottom-left.
Select "Data Services", then "Storage", then "Quick Create".

Enter a unique name for your account.
Under "Location/Affinity Group", choose a location.
Ideally, you should choose the same location as your web site or web service.
If in doubt, choose the location nearest to you.

During dev time, you probably won't want to work against production data.
To avoid this, you can create a second storage account using the steps above.

### Configure Access Keys

In order for your Node app to connect to your storage account, you'll need to configure it with its access information.
These instructions assume you've already [created an Azure Website](http://azure.microsoft.com/en-us/documentation/articles/cloud-services-nodejs-develop-deploy-express-app/).

There are two ways to configure the azure-storage module's access info.
One way is by constructing an Azure client object with a hardcoded account name and an access key.
The problem with this approach is that it requires checking your access key in with your code, which you shouldn't do.

The other appraoch is to use environment variables to store your account info.
The azure-storage module will pick up specific environment variables and use those to connect to your storage account.
Those environment variables are `AZURE_STORAGE_ACCOUNT` and `AZURE_STORAGE_ACCESS_KEY`, or `AZURE_STORAGE_CONNECTION_STRING`.
In our example, we'll use the first two.

First, in the Azure portal, navigate to your storage account.
At the bottom of the screen, click "Manage Access Keys".

{%img //itsananderson.blob.core.windows.net/post-images/azure-account-access-keys.png %}

Copy the primary access key, then navigate to your website's "Configure" tab.
Under "app settings", add two new settings:
`AZURE_STORAGE_ACCOUNT` with your storage account name, and `AZURE_STORAGE_ACCESS_KEY` with the access key you just copied.


{%img //itsananderson.blob.core.windows.net/post-images/website-access-key-configuration.png %}

For local dev, you'll also need to set up credentials for your dev storage account (or production if you didn't create a dev account).
On Linux and Mac, you can set environment variables in your `.bash_profile`.
Instructions for that are outside the scope of this post.

On Linux and Mac you can also set environment variables specifically for an app run.
This also works on Windows if you're using something like Git Bash.

```bash
AZURE_STORAGE_ACCOUNT=mystorage AZURE_STORAGE_ACCESS_KEY=123-my-access-key node app.js
```

On windows, the easiest option is to configure environment variables in your machine settings.
These instructions are for Windows 8.
You'll have to look for instructions for older versions of Windows, but the idea is pretty much the same.

1. Open the start screen.
1. Type "environment".
1. Click "Edit environment variables for your account" should come up.
1. Click the "New..." button.
1. Enter "AZURE_STORAGE_ACCOUNT" for the "Variable name" and your storage account name for the "Variable value"

{% img //itsananderson.blob.core.windows.net/post-images/storage-account-environment-variable.png %}

Repeat steps 1 through 5 again for the "AZURE_STORAGE_ACCESS_KEY" variable, then click "OK" to close the "Environment Variables" screen.
If your console is open, restart it.

### Write Some Code

Now that we've gotten the configuration taken care of, we can write some code.

We'll dive into the code more in the next post, but for now, here's something to get you started.

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
    }
});
```

If you run the above code after configuring your account credentials, it should output "Container your-container-name created".
If you run it again, it should output "Container your-container-name already exists".

Azure only creates the container if it doesn't exist.
If it already exists, the library will tell you, but it won't give you an error.

### Conclusion

We've looked at how to get set up with Azure blob storage.
In the next post, we'll talk about [creating blobs](/blog/2014/07/02/azure-blob-storage-and-node-creating-blobs/).
