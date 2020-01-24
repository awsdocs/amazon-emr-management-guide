# Configure an Output Location<a name="emr-plan-output"></a>

 The most common output format of an Amazon EMR cluster is as text files, either compressed or uncompressed\. Typically, these are written to an Amazon S3 bucket\. This bucket must be created before you launch the cluster\. You specify the S3 bucket as the output location when you launch the cluster\. 

For more information, see the following topics:

**Topics**
+ [Create and Configure an Amazon S3 Bucket](#create-s3-bucket-output)
+ [What formats can Amazon EMR return?](emr-plan-output-formats.md)
+ [How to write data to an Amazon S3 bucket you don't own](emr-s3-acls.md)
+ [Compress the Output of your Cluster](emr-plan-output-compression.md)

## Create and Configure an Amazon S3 Bucket<a name="create-s3-bucket-output"></a>

Amazon EMR \(Amazon EMR\) uses Amazon S3 to store input data, log files, and output data\. Amazon S3 refers to these storage locations as *buckets*\. Buckets have certain restrictions and limitations to conform with Amazon S3 and DNS requirements\. For more information, go to [Bucket Restrictions and Limitations](http://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html) in the *Amazon Simple Storage Service Developers Guide*\.

This section shows you how to use the Amazon S3 AWS Management Console to create and then set permissions for an Amazon S3 bucket\. However, you can also create and set permissions for an Amazon S3 bucket using the Amazon S3 API or the third\-party Curl command line tool\. For information about Curl, go to [Amazon S3 Authentication Tool for Curl](http://aws.amazon.com/code/128)\. For information about using the Amazon S3 API to create and configure an Amazon S3 bucket, go to the [Amazon Simple Storage Service API Reference](https://docs.aws.amazon.com/AmazonS3/latest/API/)\. 

**To create an Amazon S3 bucket using the console**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Choose **Create Bucket**\. 

   The **Create a Bucket** dialog box opens\.

1. Enter a bucket name, such as ***aws\-s3\-bucket1***\. 

   This name should be globally unique, and cannot be the same name used by another bucket\.

1. Select the **Region** for your bucket\. To avoid paying cross\-region bandwidth charges, create the Amazon S3 bucket in the same region as your cluster\.

   Refer to [Choose an AWS Region](emr-plan-region.md) for guidance on choosing a Region\.

1. Choose **Create**\. 

You created a bucket with the URI **s3n://*aws\-s3\-bucket1*/**\. 

**Note**  
 If you enable logging in the **Create a Bucket** wizard, it enables only bucket access logs, not cluster logs\. 

**Note**  
For more information on specifying Region\-specific buckets, refer to [Buckets and Regions](http://docs.aws.amazon.com/AmazonS3/latest/dev/LocationSelection.html) in the *Amazon Simple Storage Service Developer Guide* and [ Available Region Endpoints for the AWS SDKs ](http://aws.amazon.com/articles/3912)\.

 After you create your bucket you can set the appropriate permissions on it\. Typically, you give yourself \(the owner\) read and write access and authenticated users read access\. 

**To set permissions on an Amazon S3 bucket using the console**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** pane, open \(right\-click\) the bucket you just created\. 

1. Select **Properties**\.

1. In the **Properties** pane, select the **Permissions** tab\. 

1. Choose **Add more permissions**\.

1. Select **Authenticated Users** in the **Grantee** field\. 

1. To the right of the **Grantee** drop\-down list, select **List**\. 

1. Choose **Save**\. 

You have created a bucket and restricted permissions to authenticated users\. 

 Required Amazon S3 buckets must exist before you can create a cluster\. You must upload any required scripts or data referenced in the cluster to Amazon S3\. The following table describes example data, scripts, and log file locations\. 


| Information | Example Location on Amazon S3 | 
| --- | --- | 
| script or program |  s3://aws\-s3\-bucket1/script/MapperScript\.py  | 
| log files |  s3://aws\-s3\-bucket1/logs  | 
| input data |  s3://aws\-s3\-bucket1/input  | 
| output data |  s3://aws\-s3\-bucket1/output  | 