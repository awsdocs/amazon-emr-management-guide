# Upload Data to Amazon S3<a name="emr-plan-upload-s3"></a>

For information on how to upload objects to Amazon S3, see [ Add an Object to Your Bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg//PuttingAnObjectInABucket.html) in the *Amazon Simple Storage Service Getting Started Guide*\. For more information about using Amazon S3 with Hadoop, see [http://wiki\.apache\.org/hadoop/AmazonS3](http://wiki.apache.org/hadoop/AmazonS3)\. 

**Topics**
+ [Create and Configure an Amazon S3 Bucket](#create-s3-bucket-input)
+ [Configure Multipart Upload for Amazon S3](#Config_Multipart)
+ [Best Practices](#emr-bucket-bestpractices)

## Create and Configure an Amazon S3 Bucket<a name="create-s3-bucket-input"></a>

Amazon EMR uses the AWS SDK for Java with Amazon S3 to store input data, log files, and output data\. Amazon S3 refers to these storage locations as *buckets*\. Buckets have certain restrictions and limitations to conform with Amazon S3 and DNS requirements\. For more information, see [Bucket Restrictions and Limitations](https://docs.aws.amazon.com/AmazonS3/latest/dev//BucketRestrictions.html) in the *Amazon Simple Storage Service Developer Guide*\.

This section shows you how to use the Amazon S3 AWS Management Console to create and then set permissions for an Amazon S3 bucket\. You can also create and set permissions for an Amazon S3 bucket using the Amazon S3 API or AWS CLI\. You can also use Curl along with a modification to pass the appropriate authentication parameters for Amazon S3\.

See the following resources:
+ To create a bucket using the console, see [Create a Bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/create-bucket.html) in the *Amazon Simple Storage Service Console User Guide*\.
+ To create and work with buckets using the AWS CLI, see [Using High\-Level S3 Commands with the AWS Command Line Interface](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-s3-commands.html) in the *Amazon Simple Storage Service Console User Guide*\.
+ To create a bucket using an SDK, see [Examples of Creating a Bucket](https://docs.aws.amazon.com/AmazonS3/latest/dev/create-bucket-get-location-example.html) in the *Amazon Simple Storage Service Developer Guide*\.
+ To work with buckets using Curl, see [Amazon S3 Authentication Tool for Curl](https://aws.amazon.com/code/amazon-s3-authentication-tool-for-curl/)\.
+ For more information on specifying Region\-specific buckets, see [Accessing a Bucket](https://docs.aws.amazon.com/AmazonS3/latest/dev//UsingBucket.html#access-bucket-intro) in the *Amazon Simple Storage Service Developer Guide*\.

**Note**  
If you enable logging for a bucket, it enables only bucket access logs, not Amazon EMR cluster logs\. 

During bucket creation or after, you can set the appropriate permissions to access the bucket depending on your application\. Typically, you give yourself \(the owner\) read and write access and give authenticated users read access\.

Required Amazon S3 buckets must exist before you can create a cluster\. You must upload any required scripts or data referenced in the cluster to Amazon S3\. The following table describes example data, scripts, and log file locations\. 

## Configure Multipart Upload for Amazon S3<a name="Config_Multipart"></a>

Amazon EMR supports Amazon S3 multipart upload through the AWS SDK for Java\. Multipart upload lets you upload a single object as a set of parts\. You can upload these object parts independently and in any order\. If transmission of any part fails, you can retransmit that part without affecting other parts\. After all parts of your object are uploaded, Amazon S3 assembles the parts and creates the object\.

For more information, see [Multipart Upload Overview](https://docs.aws.amazon.com/AmazonS3/latest/dev//mpuoverview.html) in the *Amazon Simple Storage Service Developer Guide*\.

In addition, Amazon EMR offers properties that allow you to more precisely control the clean up of failed multipart upload parts\.

The following table describes the Amazon EMR configuration properties for multipart upload\. You can configure these using the `core-site` configuration classification\. For more information, see [Configure Applications](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/configure-apps.html) in the *Amazon EMR Release Guide*\.


| Configuration Parameter Name | Default Value | Description | 
| --- | --- | --- | 
| fs\.s3n\.multipart\.uploads\.enabled | true | A boolean type that indicates whether to enable multipart uploads\. When EMRFS [Consistent View](emr-plan-consistent-view.md) is enabled, multipart uploads are enabled by default and setting this value to false is ignored\. | 
| fs\.s3n\.multipart\.uploads\.split\.size | 134217728 | Specifies the maximum size of a part, in bytes, before EMRFS starts a new part upload when multipart uploads is enabled\. The minimum value is `5242880` \(5 MB\)\. If a lesser value is specified, `5242880` is used\. The maximum is `5368709120` \(5 GB\)\. If a greater value is specified, `5368709120` is used\. If EMRFS client\-side encryption is disabled and the Amazon S3 Optimized Committer is also disabled, this value also controls the maximum size that a data file can grow until EMRFS uses multipart uploads rather than a `PutObject` request to upload the file\. For more information, see  | 
| fs\.s3n\.ssl\.enabled | true | A boolean type that indicates whether to use http or https\.  | 
| fs\.s3\.buckets\.create\.enabled | true | A boolean type that indicates whether a bucket should be created if it does not exist\. Setting to false causes an exception on CreateBucket operations\. | 
| fs\.s3\.multipart\.clean\.enabled | false | A boolean type that indicates whether to enable background periodic clean up of incomplete multipart uploads\. | 
| fs\.s3\.multipart\.clean\.age\.threshold | 604800 | A long type that specifies the minimum age of a multipart upload, in seconds, before it is considered for cleanup\. The default is one week\. | 
| fs\.s3\.multipart\.clean\.jitter\.max | 10000 | An integer type that specifies the maximum amount of random jitter delay in seconds added to the 15\-minute fixed delay before scheduling next round of clean up\. | 

### Disable Multipart Upload Using the Amazon EMR Console<a name="emr-dev-multipart-upload-console"></a>

This procedure explains how to disable multipart upload using the Amazon EMR console when you create a cluster\.

**To disable multipart uploads**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Create cluster**, **Go to advanced options**\.

1. Under **Edit Software Settings** and enter the following configuration: `classification=core-site,properties=[fs.s3.multipart.uploads.enabled=false]`

1. Proceed with creating the cluster\.

### Disable Multipart Upload Using the AWS CLI<a name="emr-dev-multipart-upload-awscli"></a>

This procedure explains how to disable multipart upload using the AWS CLI\. To disable multipart upload, type the `create-cluster` command with the `--bootstrap-actions` parameter\. 

**To disable multipart upload using the AWS CLI**

1. Create a file, `myConfig.json`, with the following contents and save it in the same directory where you run the command:

   ```
   [
     {
       "Classification": "core-site",
       "Properties": {
         "fs.s3n.multipart.uploads.enabled": "false"
       }
     }
   ]
   ```

1. Type the following command and replace *myKey* with the name of your EC2 key pair\.
**Note**  
Linux line continuation characters \(\\\) are included for readability\. They can be removed or used in Linux commands\. For Windows, remove them or replace with a caret \(^\)\.

   ```
   1. aws emr create-cluster --name "Test cluster" \
   2. --release-label emr-5.23.0 --applications Name=Hive Name=Pig \
   3. --use-default-roles --ec2-attributes KeyName=myKey --instance-type m4.large \
   4. --instance-count 3 --configurations file://myConfig.json
   ```

### Disable Multipart Upload Using the API<a name="emr-dev-multipart-upload-api"></a>

For information on using Amazon S3 multipart uploads programmatically, see [Using the AWS SDK for Java for Multipart Upload](https://docs.aws.amazon.com/AmazonS3/latest/dev//UsingMPDotJavaAPI.html) in the *Amazon Simple Storage Service Developer Guide*\.

For more information about the AWS SDK for Java, see [AWS SDK for Java](https://aws.amazon.com//sdkforjava/)\.

## Best Practices<a name="emr-bucket-bestpractices"></a>

The following are recommendations for using Amazon S3 Buckets with EMR clusters\.

### Enable Versioning<a name="emr-enable-versioning"></a>

Versioning is a recommended configuration for your Amazon S3 bucket\. By enabling versioning, you ensure that even if data is unintentionally deleted or overwritten it can be recovered\. For more information, see [Using Versioning](https://docs.aws.amazon.com/AmazonS3/latest/dev/Versioning.html) in the Amazon Simple Storage Service Developer Guide\.

### Clean Up Failed Multipart Uploads<a name="emr-multipart-cleanup"></a>

EMR cluster components use multipart uploads via the AWS SDK for Java with Amazon S3 APIs to write log files and output data to Amazon S3 by default\. For information about changing properties related to this configuration using Amazon EMR, see [Configure Multipart Upload for Amazon S3](#Config_Multipart)\. Sometimes the upload of a large file can result in an incomplete Amazon S3 multipart upload\. When a multipart upload is unable to complete successfully, the in\-progress multipart upload continues to occupy your bucket and incurs storage charges\. We recommend the following options to avoid excessive file storage:
+ For buckets that you use with Amazon EMR, use a lifecycle configuration rule in Amazon S3 to remove incomplete multipart uploads three days after the upload initiation date\. Lifecycle configuration rules allow you to control the storage class and lifetime of objects\. For more information, see [Object Lifecycle Management](https://docs.aws.amazon.com/AmazonS3/latest/dev/object-lifecycle-mgmt.html), and [Aborting Incomplete Multipart Uploads Using a Bucket Lifecycle Policy](https://docs.aws.amazon.com/AmazonS3/latest/dev/mpuoverview.html#mpu-abort-incomplete-mpu-lifecycle-config)\.
+ Enable Amazon EMR's multipart cleanup feature by setting `fs.s3.multipart.clean.enabled` to `TRUE` and tuning other cleanup parameters\. This feature is useful at high volume, large scale, and with clusters that have limited uptime\. In this case, the `DaysAfterIntitiation` parameter of a lifecycle configuration rule may be too long, even if set to its minimum, causing spikes in Amazon S3 storage\. Amazon EMR's multipart cleanup allows more precise control\. For more information, see [Configure Multipart Upload for Amazon S3](#Config_Multipart)\. 

### Manage Version Markers<a name="w3aac19b9c17c13b9c13b8"></a>

We recommend that you enable a lifecycle configuration rule in Amazon S3 to remove expired object delete markers for versioned buckets that you use with Amazon EMR\. When deleting an object in a versioned bucket, a delete marker is created\. If all previous versions of the object subsequently expire, an expired object delete marker is left in the bucket\. While you are not charged for delete markers, removing expired markers can improve the performance of LIST requests\. For more information, see [Lifecycle Configuration for a Bucket with Versioning](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/lifecycle-configuration-bucket-with-versioning.html) in the Amazon Simple Storage Service Console User Guide\.

### Performance best practices<a name="w3aac19b9c17c13b9c13c10"></a>

Depending on your workloads, specific types of usage of EMR clusters and applications on those clusters can result in a high number of requests against a bucket\. For more information, see [Request Rate and Performance Considerations](https://docs.aws.amazon.com/AmazonS3/latest/dev/request-rate-perf-considerations.html) in the *Amazon Simple Storage Service Developer Guide*\. 