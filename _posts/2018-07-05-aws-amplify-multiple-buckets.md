---
layout: post
title: Using Multiple Buckets with the AWS Amplify Storage Module
---

I'm currently working with React and AWS Amplify to create a front end for a web application. The app allows users to upload images, which are subsequently resized with a lambda function (post to follow) and placed into a new S3 bucket. This means the app needs to be able to upload to one bucket and get images from another. Weirdly the AWS Amplify documentation covers setup for a single bucket using global configuration. There is an open [Github Issue](https://github.com/aws/aws-amplify/issues/329) on the Amplify project, but I had a hunt around and found a quick solution already in the code.

AWS Amplify is a Javascript library that provides easier ways to interact with AWS services often used by app developers, such as API Gateway and S3.

The documentation for the [Storage module](https://aws.github.io/aws-amplify/media/storage_guide), that interacts with S3, suggests the following code for configuring a bucket to use.

```javascript
Amplify.configure({
    Auth: {
        identityPoolId: 'XX-XXXX-X:XXXXXXXX-XXXX-1234-abcd-1234567890ab', //REQUIRED - Amazon Cognito Identity Pool ID
        region: 'XX-XXXX-X', // REQUIRED - Amazon Cognito Region
        userPoolId: 'XX-XXXX-X_abcd1234', //OPTIONAL - Amazon Cognito User Pool ID
        userPoolWebClientId: 'XX-XXXX-X_abcd1234', //OPTIONAL - Amazon Cognito Web Client ID
    },
    Storage: {
        bucket: '', //REQUIRED -  Amazon S3 bucket
        region: 'XX-XXXX-X', //OPTIONAL -  Amazon service region
    }
});
```

As you can see `bucket` is mandatory. When carrying out some of the operations detailed in the documentation such as puts and gets, this is the bucket that gets used.

However, there are a few more options available for each operation than the documentation outlines.

For example under the Get section of the documentation, the `expires` option is explained.

```javascript
Storage.get('test.txt', {expires: 60})
    .then(result => console.log(result))
    .catch(err => console.log(err));
```

The `expires` keywork is contained in the Get call. Other than fooling around with React in an attempt to throw together a front end, I am not a Javascript developer. But the `expires` keyword here seems to work similarly to `kwargs` in Python.

Diving into the code and looking at the Get and Put operations I found this snippet in the [Storage code](https://github.com/aws/aws-amplify/blob/master/packages/aws-amplify/src/Storage/Storage.ts).

```javascript
public async get(key: string, options?): Promise<Object> {
    ...
    const opt = Object.assign({}, this._options, options);
    const { bucket, region, credentials, level, download, track, expires } = opt;
    ...
    const params: any = {
            Bucket: bucket,
            Key: final_key
    };
```

The options here expose an optional bucket value. So I had a go at setting that in my code and it works!

```javascript
Storage.vault.get(this.props.image_key, 
    {
        bucket: 'alternative-bucket-name'
    }
);
```

So as it happens it is possible to use multiple S3 buckets with AWS Amplify. Hopefully the documentation will be updated soon to reflect this. If I get time I may even make a pull request myself.