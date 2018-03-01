# Step 1: Set Up Prerequisites for Your Sample Cluster<a name="emr-gs-prerequisites"></a>

 Before you begin setting up your Amazon EMR cluster, make sure that you complete the prerequisites in this topic\. 


+ [Sign Up for AWS](#emr-gs-signup)
+ [Create an Amazon S3 Bucket](#emr-gs-create-bucket)
+ [Create an Amazon EC2 Key Pair](#emr-gs-key-pair)

## Sign Up for AWS<a name="emr-gs-signup"></a>

If you do not have an AWS account, use the following procedure to create one\.

**To sign up for AWS**

1. Open [https://aws\.amazon\.com/](https://aws.amazon.com/) and choose **Create an AWS Account**\.

1. Follow the online instructions\.

## Create an Amazon S3 Bucket<a name="emr-gs-create-bucket"></a>

 In this tutorial, you use an Amazon S3 bucket to store your log files and output data\. Because of Hadoop requirements, S3 bucket names used with Amazon EMR have the following constraints: 

+ Must contain only lowercase letters, numbers, periods \(\.\), and hyphens \(\-\)

+ Cannot end in numbers

If you already have a bucket that meets these requirements, you can use it for this tutorial\. Otherwise, create a bucket to use\. For more information about creating buckets, go to [Create a Bucket](http://docs.aws.amazon.com/AmazonS3/latest/UG/CreatingABucket.html) in the *Amazon Simple Storage Service Getting Started Guide*\.

In your S3 bucket, create folders named `logs` and `output`\. Also, the output folder should be empty\. For more information about creating folders, go to [Creating A Folder](http://docs.aws.amazon.com/AmazonS3/latest/UG/CreatingaFolder.html) in the *Amazon Simple Storage Service Console User Guide*\.

## Create an Amazon EC2 Key Pair<a name="emr-gs-key-pair"></a>

You must have an Amazon Elastic Compute Cloud \(Amazon EC2\) key pair to connect to the nodes in your cluster over a secure channel using the Secure Shell \(SSH\) protocol\. If you already have a key pair that you want to use, you can skip this step\. If you don't have a key pair, follow one of the following procedures depending on your operating system\. 

+ [Creating Your Key Pair Using Amazon EC2](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-key-pairs.html#having-ec2-create-your-key-pair) in the *Amazon EC2 User Guide for Windows Instances*

+ [Creating Your Key Pair Using Amazon EC2](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html#having-ec2-create-your-key-pair) in the *Amazon EC2 User Guide for Linux Instances*
**Note**  
Use this procedure for both Linux and Mac OS X operating systems\.