# Specifying server\-side encryption with AWS KMS \(SSE\-KMS\)<a name="specifying-kms-encryption"></a>

When you create an object, you can specify the use of server\-side encryption with AWS Key Management Service \(AWS KMS\) customer master keys \(CMKs\) to encrypt your data\. This is true when you are either uploading a new object or copying an existing object\. This encryption is known as SSE\-KMS\. 

You can specify SSE\-KMS using the S3 console, REST APIs, AWS SDKs, and AWS CLI\. For more information, see the topics below\. 

## Using the S3 console<a name="add-object-encryption-kms"></a>

This topic describes how to set or change the type of encryption an object using the Amazon S3 console\.

**Note**  
If you change an object's encryption, a new object is created to replace the old one\. If S3 Versioning is enabled, a new version of the object is created, and the existing object becomes an older version\. The role that changes the property also becomes the owner of the new object or \(object version\)\. 

**To add or change encryption for an object**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that contains the object\.

1. In the **Objects** list, choose the name of the object that you want to add or change encryption for\.

   The **Object overview** opens, displaying the properties for your object\.

1. Under **Server\-side encryption settings**, choose **Edit**\.

   The **Edit server\-side encryption** page opens

1. To enable server\-side encryption for your object, under **Server\-side encryption**, choose **Enable**\.

1. Under **Encryption key type**, choose **AWS Key Management Service key \(SSE\-KMS\)**\.
**Important**  
If you use the AWS KMS option for your default encryption configuration, you are subject to the RPS \(requests per second\) limits of AWS KMS\. For more information about AWS KMS limits and how to request a limit increase, see [AWS KMS limits](https://docs.aws.amazon.com/kms/latest/developerguide/limits.html)\. 

1. Under **AWS KMS key**, choose one of the following:
   + **AWS managed key \(aws/s3\)**
   + **Choose from your KMS master keys**, and choose your **KMS master key**\.
   + **Enter KMS master key ARN**, and enter your AWS KMS key ARN\.
**Important**  
You can only use KMS CMKs that are enabled in the same AWS Region as the bucket\. When you choose **Choose from your KMS master keys**, the S3 console only lists 100 KMS CMKs per Region\. If you have more than 100 CMKs in the same Region, you can only see the first 100 CMKs in the S3 console\. To use a KMS CMK that is not listed in the console, choose **Custom KMS ARN**, and enter the KMS CMK ARN\.  
When you use an AWS KMS CMK for server\-side encryption in Amazon S3, you must choose a CMK that is enabled in the same Region as your bucket\. Additionally, Amazon S3 only supports symmetric CMKs and not asymmetric CMKs\. For more information, see [Using Symmetric and Asymmetric Keys](https://docs.aws.amazon.com/kms/latest/developerguide/symmetric-asymmetric.html) in the *AWS Key Management Service Developer Guide*\.

   For more information about creating an AWS KMS CMK, see [Creating Keys](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html) in the *AWS Key Management Service Developer Guide*\. For more information about using AWS KMS with Amazon S3, see [Protecting Data Using Server\-Side Encryption with CMKs Stored in AWS Key Management Service \(SSE\-KMS\)](UsingKMSEncryption.md)\.

1. Choose **Save changes**\.

**Note**  
This action applies encryption to all specified objects\. When encrypting folders, wait for the save operation to finish before adding new objects to the folder\.

## Using the REST API<a name="KMSUsingRESTAPI"></a>

When you create an object—that is, when you upload a new object or copy an existing object—you can specify the use of server\-side encryption with AWS Key Management Service \(AWS KMS\) customer master keys \(CMKs\) to encrypt your data\. To do this, add the `x-amz-server-side-encryption` header to the request\. Set the value of the header to the encryption algorithm `aws:kms`\. Amazon S3 confirms that your object is stored using SSE\-KMS by returning the response header `x-amz-server-side-encryption`\. 

If you specify the `x-amz-server-side-encryption` header with a value of `aws:kms`, you can also use the following request headers:
+ `x-amz-server-side-encryption-aws-kms-key-id`
+ `x-amz-server-side-encryption-context`
+ `x-amz-server-side-encryption-bucket-key-enabled`

**Topics**
+ [Amazon S3 REST APIs that support SSE\-KMS](#sse-request-headers-kms)
+ [Encryption context \(x\-amz\-server\-side\-encryption\-context\)](#s3-kms-encryption-context)
+ [AWS KMS key ID \(x\-amz\-server\-side\-encryption\-aws\-kms\-key\-id\)](#s3-kms-key-id-api)
+ [S3 Bucket Keys \(x\-amz\-server\-side\-encryption\-aws\-bucket\-key\-enabled\)](#bucket-key-api)

### Amazon S3 REST APIs that support SSE\-KMS<a name="sse-request-headers-kms"></a>

The following REST APIs accept the `x-amz-server-side-encryption`, `x-amz-server-side-encryption-aws-kms-key-id`, and `x-amz-server-side-encryption-context` request headers\.
+ [PUT Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPUT.html) — When you upload data using the PUT API, you can specify these request headers\. 
+ [PUT Object \- Copy](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectCOPY.html)— When you copy an object, you have both a source object and a target object\. When you pass SSE\-KMS headers with the COPY operation, they are applied only to the target object\. When copying an existing object, regardless of whether the source object is encrypted or not, the destination object is not encrypted unless you explicitly request server\-side encryption\.
+ [POST Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPOST.html)— When you use a POST operation to upload an object, instead of the request headers, you provide the same information in the form fields\.
+ [Initiate Multipart Upload](https://docs.aws.amazon.com/AmazonS3/latest/API/mpUploadInitiate.html)— When you upload large objects using the multipart upload API, you can specify these headers\. You specify these headers in the initiate request\.

The response headers of the following REST APIs return the `x-amz-server-side-encryption` header when an object is stored using server\-side encryption\.
+ [PUT Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPUT.html)
+ [PUT Object \- Copy](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectCOPY.html)
+ [POST Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPOST.html)
+ [Initiate Multipart Upload](https://docs.aws.amazon.com/AmazonS3/latest/API/mpUploadInitiate.html)
+ [Upload Part](https://docs.aws.amazon.com/AmazonS3/latest/API/mpUploadUploadPart.html)
+ [Upload Part \- Copy](https://docs.aws.amazon.com/AmazonS3/latest/API/mpUploadUploadPartCopy.html)
+ [Complete Multipart Upload](https://docs.aws.amazon.com/AmazonS3/latest/API/mpUploadComplete.html)
+ [Get Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectGET.html)
+ [Head Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectHEAD.html)

**Important**  
All GET and PUT requests for an object protected by AWS KMS will fail if you don't make them using Secure Sockets Language \(SSL\) or Signature Version 4\.
If your object uses SSE\-KMS, don't send encryption request headers for `GET` requests and `HEAD` requests, or you’ll get an HTTP 400 BadRequest error\.

### Encryption context \(x\-amz\-server\-side\-encryption\-context\)<a name="s3-kms-encryption-context"></a>

If you specify `x-amz-server-side-encryption:aws:kms`, the Amazon S3 API supports an encryption context with the `x-amz-server-side-encryption-context` header\. An encryption context is an optional set of key\-value pairs that can contain additional contextual information about the data\. 

In Amazon S3, the object or bucket Amazon Resource Name \(ARN\) is commonly used as an encryption context\. If you use SSE\-KMS without enabling an S3 Bucket Key, you use the object ARN as your encryption context, for example, `arn:aws:s3:::object_ARN`\. However, if you use SSE\-KMS and enable an S3 Bucket Key, you use the bucket ARN for your encryption context, for example, `arn:aws:s3:::bucket_ARN`\. 

For information about the encryption context in Amazon S3, see [Encryption context](UsingKMSEncryption.md#encryption-context)\. For general information about encryption context, see [AWS Key Management Service Concepts \- Encryption Context](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#encrypt_context) in the *AWS Key Management Service Developer Guide*\. 

### AWS KMS key ID \(x\-amz\-server\-side\-encryption\-aws\-kms\-key\-id\)<a name="s3-kms-key-id-api"></a>

You can use the `x-amz-server-side-encryption-aws-kms-key-id` header to specify the ID of the customer managed CMK used to protect the data\. If you specify `x-amz-server-side-encryption:aws:kms`, but don't provide `x-amz-server-side-encryption-aws-kms-key-id`, Amazon S3 uses the AWS managed CMK in AWS KMS to protect the data\. If you want to use a customer managed AWS KMS CMK, you must provide the `x-amz-server-side-encryption-aws-kms-key-id` of the customer managed CMK\.

**Important**  
When you use an AWS KMS CMK for server\-side encryption in Amazon S3, you must choose a symmetric CMK\. Amazon S3 only supports symmetric CMKs and not asymmetric CMKs\. For more information, see [Using Symmetric and Asymmetric Keys](https://docs.aws.amazon.com/kms/latest/developerguide/symmetric-asymmetric.html) in the *AWS Key Management Service Developer Guide*\.

### S3 Bucket Keys \(x\-amz\-server\-side\-encryption\-aws\-bucket\-key\-enabled\)<a name="bucket-key-api"></a>

You can use the `x-amz-server-side-encryption-aws-bucket-key-enabled` request header to enable or disable an S3 Bucket Key at the object\-level\. S3 Bucket Keys can reduce your AWS KMS request costs by decreasing the request traffic from Amazon S3 to AWS KMS\. For more information, see [Reducing the cost of SSE\-KMS with Amazon S3 Bucket Keys](bucket-key.md)\.

If you specify `x-amz-server-side-encryption:aws:kms`, but don't provide `x-amz-server-side-encryption-aws-bucket-key-enabled`, your object uses the S3 Bucket Key settings for the destination bucket to encrypt your object\. For more information, see [Configuring an S3 Bucket Key at the object level using the REST API, AWS SDKs, or AWS CLI](configuring-bucket-key-object.md)\.

## Using the AWS SDKs<a name="kms-using-sdks"></a>

When using AWS SDKs, you can request Amazon S3 to use AWS Key Management Service \(AWS KMS\) customer master keys \(CMKs\)\. This section provides examples of using the AWS SDKs for Java and \.NET\. For information about other SDKs, go to [Sample Code and Libraries](https://aws.amazon.com/code)\.

**Important**  
When you use an AWS KMS CMK for server\-side encryption in Amazon S3, you must choose a symmetric CMK\. Amazon S3 only supports symmetric CMKs and not asymmetric CMKs\. For more information, see [Using Symmetric and Asymmetric Keys](https://docs.aws.amazon.com/kms/latest/developerguide/symmetric-asymmetric.html) in the *AWS Key Management Service Developer Guide*\.

### Copy operation<a name="kms-copy-operation"></a>

 When copying objects, you add the same request properties \(`ServerSideEncryptionMethod` and `ServerSideEncryptionKeyManagementServiceKeyId`\) to request Amazon S3 to use an AWS KMS CMK\. For more information about copying objects, see [Copying objects](copy-object.md)\. 

### Put operation<a name="kms-put-operation"></a>

------
#### [ Java ]

When uploading an object using the AWS SDK for Java, you can request Amazon S3 to use an AWS KMS CMK by adding the `SSEAwsKeyManagementParams` property as shown in the following request\.

```
PutObjectRequest putRequest = new PutObjectRequest(bucketName,
   keyName, file).withSSEAwsKeyManagementParams(new SSEAwsKeyManagementParams());
```

In this case, Amazon S3 uses the AWS managed CMK \(see [Using Server\-Side Encryption with CMKs Stored in AWS KMS ](UsingKMSEncryption.md)\)\. You can optionally create a symmetric customer managed CMK and specify that in the request\.

```
PutObjectRequest putRequest = new PutObjectRequest(bucketName,
   keyName, file).withSSEAwsKeyManagementParams(new SSEAwsKeyManagementParams(keyID));
```

For more information about creating customer managed CMKs, see [Programming the AWS KMS API](https://docs.aws.amazon.com/kms/latest/developerguide/programming-top.html) in the *AWS Key Management Service Developer Guide*\.

For working code examples of uploading an object, see the following topics\. You will need to update those code examples and provide encryption information as shown in the preceding code fragment\.
+ For uploading an object in a single operation, see [Uploading objects](upload-objects.md)\.
+ For a multipart upload, see the following topics:
  + Using high\-level multipart upload API, see [Uploading an object using multipart upload](mpu-upload-object.md)\. 
  + If you are using the low\-level multipart upload API, see [Using the AWS SDKs \(low\-level\-level API\)](mpu-upload-object.md#mpu-upload-low-level)\.

------
#### [ \.NET ]

When uploading an object using the AWS SDK for \.NET, you can request Amazon S3 to use an AWS KMS CMK by adding the `ServerSideEncryptionMethod` property as shown in the following request\.

```
PutObjectRequest putRequest = new PutObjectRequest
 {
     BucketName = bucketName,
     Key = keyName,
     // other properties.
     ServerSideEncryptionMethod = ServerSideEncryptionMethod.AWSKMS
 };
```

In this case, Amazon S3 uses the AWS managed CMK\. For more information, see [Protecting Data Using Server\-Side Encryption with CMKs Stored in AWS Key Management Service \(SSE\-KMS\)](UsingKMSEncryption.md)\. You can optionally create your own symmetric customer managed CMK and specify that in the request\. 

```
PutObjectRequest putRequest1 = new PutObjectRequest
{
  BucketName = bucketName,
  Key = keyName,
  // other properties.
  ServerSideEncryptionMethod = ServerSideEncryptionMethod.AWSKMS,
  ServerSideEncryptionKeyManagementServiceKeyId = keyId
};
```

For more information about creating customer managed CMKs, see [Programming the AWS KMS API](https://docs.aws.amazon.com/kms/latest/developerguide/programming-top.html) in the *AWS Key Management Service Developer Guide*\. 

For working code examples of uploading an object, see the following topics\. You will need to update these code examples and provide encryption information as shown in the preceding code fragment\.
+ For uploading an object in a single operation, see [Uploading objects](upload-objects.md)\.
+ For multipart upload see the following topics:
  + Using high\-level multipart upload API, see [Uploading an object using multipart upload](mpu-upload-object.md)\. 
  + Using low\-level multipart upload API, see [Uploading an object using multipart upload](mpu-upload-object.md)\.

------

### Presigned URLs<a name="kms-presigned-urls"></a>

------
#### [ Java ]

When creating a presigned URL for an object encrypted using an AWS KMS CMK, you must explicitly specify Signature Version 4\.

```
ClientConfiguration clientConfiguration = new ClientConfiguration();
clientConfiguration.setSignerOverride("AWSS3V4SignerType");
AmazonS3Client s3client = new AmazonS3Client(
        new ProfileCredentialsProvider(), clientConfiguration);
...
```

For a code example, see [Sharing an object with a presigned URL](ShareObjectPreSignedURL.md)\. 

------
#### [ \.NET ]

When creating a presigned URL for an object encrypted using an AWS KMS CMK, you must explicitly specify Signature Version 4\.

```
AWSConfigs.S3Config.UseSignatureVersion4 = true;
```

For a code example, see [Sharing an object with a presigned URL](ShareObjectPreSignedURL.md)\.

------