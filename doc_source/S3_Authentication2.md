# Authenticating requests using the REST API<a name="S3_Authentication2"></a>

When accessing Amazon S3 using REST, you must provide the following items in your request so the request can be authenticated: 

**Request elements**
+ **AWS access key Id** – Each request must contain the access key ID of the identity you are using to send your request\. 
+ **Signature** – Each request must contain a valid request signature, or the request is rejected\. 

  A request signature is calculated using your secret access key, which is a shared secret known only to you and AWS\.
+ **Time stamp** – Each request must contain the date and time the request was created, represented as a string in UTC\. 
+ **Date** – Each request must contain the time stamp of the request\. 

  Depending on the API action you're using, you can provide an expiration date and time for the request instead of or in addition to the time stamp\. See the authentication topic for the particular action to determine what it requires\.

Following are the general steps for authenticating requests to Amazon S3\. It is assumed you have the necessary security credentials, access key ID and secret access key\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/HMACAuthProcess_You.png)


|  |  | 
| --- |--- |
|  1  |  Construct a request to AWS\.  | 
|  2  |  Calculate the signature using your secret access key\.  | 
|  3  |  Send the request to Amazon S3\. Include your access key ID and the signature in your request\. Amazon S3 performs the next three steps\.  | 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/HMACAuthProcess_AWS.png)


|  |  | 
| --- |--- |
|  4  |  Amazon S3 uses the access key ID to look up your secret access key\.  | 
|  5  |  Amazon S3 calculates a signature from the request data and the secret access key using the same algorithm that you used to calculate the signature you sent in the request\.  | 
|  6  |  If the signature generated by Amazon S3 matches the one you sent in the request, the request is considered authentic\. If the comparison fails, the request is discarded, and Amazon S3 returns an error response\.  | 

## Detailed authentication information<a name="S3_Authentication_DetailedAuthentication"></a>

For detailed information about REST authentication, see [Signing and authenticating REST requests](RESTAuthentication.md)\.