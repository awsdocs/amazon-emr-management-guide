# Step 1: Set Up Prerequisites for Your Sample Cluster<a name="emr-gs-prerequisites"></a>

Before you begin setting up your Amazon EMR cluster, make sure that you complete the prerequisites in this topic\. 

## Sign Up for AWS<a name="emr-gs-signup"></a>

If you do not have an AWS account, use the following procedure to create one\.

**To sign up for AWS**

1. Open [https://aws\.amazon\.com/](https://aws.amazon.com/), and then choose **Create an AWS Account**\.
**Note**  
If you previously signed in to the AWS Management Console using AWS account root user credentials, choose **Sign in to a different account**\. If you previously signed in to the console using IAM credentials, choose **Sign\-in using root account credentials**\. Then choose **Create a new AWS account**\.

1. Follow the online instructions\.

   Part of the sign\-up procedure involves receiving a phone call and entering a verification code using the phone keypad\.

## Create an Amazon S3 Bucket<a name="emr-gs-create-bucket"></a>

 In this tutorial, you specify an Amazon S3 bucket and folder to store the output data from a Hive query\. The tutorial uses the default log location, but you can also specify a custom location if you prefer\. Because of Hadoop requirements, bucket and folder names that you use with Amazon EMR have the following limitations:
+ They must contain only lowercase letters, numbers, periods \(\.\), and hyphens \(\-\)\.
+ They cannot end in numbers\.

If you already have access to a folder that meets these requirements, you can use it for this tutorial\. The output folder should be empty\. Another requirement to remember is that bucket names must be unique *across all AWS accounts*\. 

For more information about creating a bucket, see [Create a Bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html) in the *Amazon Simple Storage Service Getting Started Guide*\. After you create the bucket, choose it from the list and then choose **Create folder**, replace **New folder** with a name that meets the requirements, and then choose **Save**\.

The bucket and folder name used later in the tutorial is *s3://mybucket/MyHiveQueryResults*\.

## Create an Amazon EC2 Key Pair<a name="emr-gs-key-pair"></a>

You must have an Amazon Elastic Compute Cloud \(Amazon EC2\) key pair to connect to the nodes in your cluster over a secure channel using the Secure Shell \(SSH\) protocol\. If you already have a key pair that you want to use, you can skip this step\. If you don't have a key pair, follow one of the following procedures depending on your operating system\. 
+ [Creating Your Key Pair Using Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-key-pairs.html#having-ec2-create-your-key-pair) in the *Amazon EC2 User Guide for Windows Instances*
+ [Creating Your Key Pair Using Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html#having-ec2-create-your-key-pair) in the *Amazon EC2 User Guide for Linux Instances*\. Use this procedure for Mac OS as well\.