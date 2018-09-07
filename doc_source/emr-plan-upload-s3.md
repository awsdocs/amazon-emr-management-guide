# Upload Data to Amazon S3<a name="emr-plan-upload-s3"></a>

For information on how to upload objects to Amazon S3, see [ Add an Object to Your Bucket](http://docs.aws.amazon.com/AmazonS3/latest/gsg//PuttingAnObjectInABucket.html) in the *Amazon Simple Storage Service Getting Started Guide*\. For more information about using Amazon S3 with Hadoop, see [http://wiki\.apache\.org/hadoop/AmazonS3](http://wiki.apache.org/hadoop/AmazonS3)\. 

**Topics**
+ [Create and Configure an Amazon S3 Bucket](#create-s3-bucket-input)
+ [Best Practices](#emr-bucket-bestpractices)
+ [Configure Multipart Upload for Amazon S3](#Config_Multipart)

## Create and Configure an Amazon S3 Bucket<a name="create-s3-bucket-input"></a>

Amazon EMR uses the AWS SDK for Java with Amazon S3 to store input data, log files, and output data\. Amazon S3 refers to these storage locations as *buckets*\. Buckets have certain restrictions and limitations to conform with Amazon S3 and DNS requirements\. For more information, see [Bucket Restrictions and Limitations](http://docs.aws.amazon.com/AmazonS3/latest/dev//BucketRestrictions.html) in the *Amazon Simple Storage Service Developer Guide*\.

This section shows you how to use the Amazon S3 AWS Management Console to create and then set permissions for an Amazon S3 bucket\. You can also create and set permissions for an Amazon S3 bucket using the Amazon S3 API or AWS CLI\. You can also use Curl along with a modification to pass the appropriate authentication parameters for Amazon S3\.

See the following resources:
+ To create a bucket using the console, see [Create a Bucket](http://docs.aws.amazon.com/AmazonS3/latest/gsg/create-bucket.html) in the *Amazon Simple Storage Service Console User Guide*\.
+ To create and work with buckets using the AWS CLI, see [Using High\-Level S3 Commands with the AWS Command Line Interface](http://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-s3-commands.html) in the *Amazon Simple Storage Service Console User Guide*\.
+ To create a bucket using an SDK, see [Examples of Creating a Bucket](http://docs.aws.amazon.com/AmazonS3/latest/dev/create-bucket-get-location-example.html) in the *Amazon Simple Storage Service Developer Guide*\.
+ To work with buckets using Curl, see [Amazon S3 Authentication Tool for Curl](https://aws.amazon.com/code/amazon-s3-authentication-tool-for-curl/)\.
+ For more information on specifying Region\-specific buckets, see [Accessing a Bucket](http://docs.aws.amazon.com/AmazonS3/latest/dev//UsingBucket.html#access-bucket-intro) in the *Amazon Simple Storage Service Developer Guide*\.

**Note**  
If you enable logging for a bucket, it enables only bucket access logs, not Amazon EMR cluster logs\. 

During bucket creation or after, you can set the appropriate permissions to access the bucket depending on your application\. Typically, you give yourself \(the owner\) read and write access and give authenticated users read access\.

Required Amazon S3 buckets must exist before you can create a cluster\. You must upload any required scripts or data referenced in the cluster to Amazon S3\. The following table describes example data, scripts, and log file locations\. 

## Best Practices<a name="emr-bucket-bestpractices"></a>

The following are recommendations for using Amazon S3 Buckets with EMR clusters\.

### Enable Versioning<a name="w3aac17b9c17c13b9c11b4"></a>

Versioning is a recommended configuration for your Amazon S3 bucket\. By enabling versioning, you ensure that even if data is unintentionally deleted or overwritten it can be recovered\. For more information, go to [Using Versioning](http://docs.aws.amazon.com/AmazonS3/latest/dev/Versioning.html) in the Amazon Simple Storage Service Developer Guide\.

### Consider Lifecycle Management<a name="emr-multipart-lifecycle"></a>

Lifecycle Management in Amazon S3 allows you to create rules which control the storage class and lifetime of your objects\. For more information, see [Object Lifecycle Management](http://docs.aws.amazon.com/AmazonS3/latest/dev/object-lifecycle-mgmt.html)

### Clean Up Failed Multipart Uploads and Version Markers<a name="w3aac17b9c17c13b9c11b8"></a>

EMR cluster components use multipart uploads via the AWS SDK for Java with Amazon S3 APIs to write log files and output data to Amazon S3 by default\. For information about changing this configuration, see [Configure Multipart Upload for Amazon S3](#Config_Multipart)\. Amazon EMR does not automatically manage incomplete multipart uploads\. Sometimes the upload of a large file can result in an incomplete Amazon S3 multipart upload\. When a multipart upload is unable to complete successfully, the in\-progress multipart upload continues to occupy your bucket and incurs storage charges\. We recommend that for buckets you use with Amazon EMR, you use a lifecycle rule to remove incomplete multipart uploads three days after the upload initiation date\. For more information, see [Aborting Incomplete Multipart Uploads Using a Bucket Lifecycle Policy](http://docs.aws.amazon.com/AmazonS3/latest/dev/mpuoverview.html#mpu-abort-incomplete-mpu-lifecycle-config)\.

When deleting an object in a versioned bucket a delete marker is created\. If all previous versions of the object subsequently expire, an expired object delete marker is left within the bucket\. While there is no charge for these delete markers, removing the expired delete markers can improve the performance of LIST requests\. It is recommended that for versioned buckets you will use with Amazon EMR, you should also enable a rule to remove expired object delete markers\. For more information, see [Lifecycle Configuration for a Bucket with Versioning](http://docs.aws.amazon.com/AmazonS3/latest/user-guide/lifecycle-configuration-bucket-with-versioning.html) in the Amazon Simple Storage Service Console User Guide\.

### Performance best practices<a name="w3aac17b9c17c13b9c11c10"></a>

Depending on your workloads, specific types of usage of EMR clusters and applications on those clusters can result in a high number of requests against a bucket\. For more information, see [Request Rate and Performance Considerations](http://docs.aws.amazon.com/AmazonS3/latest/dev/request-rate-perf-considerations.html) in the *Amazon Simple Storage Service Developer Guide*\. 

## Configure Multipart Upload for Amazon S3<a name="Config_Multipart"></a>

Amazon EMR supports Amazon S3 multipart upload through the AWS SDK for Java\. Multipart upload lets you upload a single object as a set of parts\. You can upload these object parts independently and in any order\. If transmission of any part fails, you can retransmit that part without affecting other parts\. After all parts of your object are uploaded, Amazon S3 assembles the parts and creates the object\.

For more information on Amazon S3 multipart uploads, go to [Uploading Objects Using Multipart Upload](http://docs.aws.amazon.com/AmazonS3/latest/dev//mpuoverview.html) in the *Amazon Simple Storage Service Developer Guide*\.

The Amazon EMR configuration parameters for multipart upload are described in the following table\.


| Configuration Parameter Name | Default Value | Description | 
| --- | --- | --- | 
| fs\.\[s3\|s3n\]\.multipart\.uploads\.enabled | True | A boolean type that indicates whether to enable multipart uploads\. | 
| fs\.s3n\.ssl\.enabled | True | A boolean type that indicates whether to use http or https\.  | 
| fs\.s3\.buckets\.create\.enabled | True | This setting automatically creates a bucket if it doesn't exist\. Setting to False will cause an exception on CreateBucket operations for that case\. | 

You modify the configuration parameters for multipart uploads using a bootstrap action\.

### Disable Multipart Upload Using the Amazon EMR Console<a name="emr-dev-multipart-upload-console"></a>

This procedure explains how to disable multipart upload using the Amazon EMR console\. 

**To disable multipart uploads with a bootstrap action using the console**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Create cluster**\.

1. Choose **Go to advanced options**\.

1. Choose **Edit Software Settings** and enter the following configuration: `classification=core-site,properties=[fs.s3.multipart.uploads.enabled=false]`

1. Proceed with creating the cluster as described in [Plan and Configure Clusters](emr-plan.md)\.

### Disable Multipart Upload Using the AWS CLI<a name="emr-dev-multipart-upload-awscli"></a>

This procedure explains how to disable multipart upload using the AWS CLI\. To disable multipart upload, type the `create-cluster` command with the `--bootstrap-actions` parameter\. 

**To disable multipart upload using the AWS CLI**

1. Create a file, `myConfig.json`, with the following contents:

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

   ```
   1. aws emr create-cluster --name "Test cluster" --release-label emr-4.0.0 --applications Name=Hive Name=Pig --use-default-roles --ec2-attributes KeyName=myKey --instance-type m4.large --instance-count 3 --configurations file://./myConfig.json
   ```

### Disable Multipart Upload Using the API<a name="emr-dev-multipart-upload-api"></a>

For information on using Amazon S3 multipart uploads programmatically, go to [Using the AWS SDK for Java for Multipart Upload](http://docs.aws.amazon.com/AmazonS3/latest/dev//UsingMPDotJavaAPI.html) in the *Amazon Simple Storage Service Developer Guide*\.

For more information about the AWS SDK for Java, go to the [AWS SDK for Java](https://aws.amazon.com//sdkforjava/) detail page\. 