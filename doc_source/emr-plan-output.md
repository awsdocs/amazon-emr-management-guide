# Configure an output location<a name="emr-plan-output"></a>

 The most common output format of an Amazon EMR cluster is as text files, either compressed or uncompressed\. Typically, these are written to an Amazon S3 bucket\. This bucket must be created before you launch the cluster\. You specify the S3 bucket as the output location when you launch the cluster\. 

For more information, see the following topics:

**Topics**
+ [Create and configure an Amazon S3 bucket](#create-s3-bucket-output)
+ [What formats can Amazon EMR return?](emr-plan-output-formats.md)
+ [How to write data to an Amazon S3 bucket you don't own](emr-s3-acls.md)
+ [Compress the output of your cluster](emr-plan-output-compression.md)

## Create and configure an Amazon S3 bucket<a name="create-s3-bucket-output"></a>

Amazon EMR \(Amazon EMR\) uses Amazon S3 to store input data, log files, and output data\. Amazon S3 refers to these storage locations as *buckets*\. Buckets have certain restrictions and limitations to conform with Amazon S3 and DNS requirements\. For more information, go to [Bucket Restrictions and Limitations](https://docs.aws.amazon.com/AmazonS3/latest/userguide/BucketRestrictions.html) in the *Amazon Simple Storage Service Developers Guide*\.

To create a an Amazon S3 bucket, follow the instructions on the [Creating a bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/create-bucket-overview.html) page in the *Amazon Simple Storage Service Developers Guide*\.

**Note**  
 If you enable logging in the **Create a Bucket** wizard, it enables only bucket access logs, not cluster logs\. 

**Note**  
For more information on specifying Region\-specific buckets, refer to [Buckets and Regions](https://docs.aws.amazon.com/AmazonS3/latest/dev/LocationSelection.html) in the *Amazon Simple Storage Service Developer Guide* and [ Available Region Endpoints for the AWS SDKs ](https://aws.amazon.com/articles/available-region-endpoints-for-the-aws-sdks/)\.

 After you create your bucket you can set the appropriate permissions on it\. Typically, you give yourself \(the owner\) read and write access\. We strongly recommend that you follow [Security Best Practices for Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/security-best-practices.html) when configuring your bucket\. 

 Required Amazon S3 buckets must exist before you can create a cluster\. You must upload any required scripts or data referenced in the cluster to Amazon S3\. The following table describes example data, scripts, and log file locations\. 


| Information | Example Location on Amazon S3 | 
| --- | --- | 
| script or program |  s3://DOC\-EXAMPLE\-BUCKET1/script/MapperScript\.py  | 
| log files |  s3://DOC\-EXAMPLE\-BUCKET1/logs  | 
| input data |  s3://DOC\-EXAMPLE\-BUCKET1/input  | 
| output data |  s3://DOC\-EXAMPLE\-BUCKET1/output  | 