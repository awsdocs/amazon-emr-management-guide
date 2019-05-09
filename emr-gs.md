# Getting Started: Analyzing Big Data with Amazon EMR<a name="emr-gs"></a>

This tutorial walks you through the process of creating a sample Amazon EMR cluster using **Quick Create** options in the AWS Management Console\. After you create the cluster, you submit a Hive script as a step to process sample data stored in Amazon Simple Storage Service \(Amazon S3\)\.

This tutorial is not meant for production environments, and it does not cover configuration options in depth\. It is meant to help you set up a cluster for evaluation purposes as quickly as possible\. If you have questions or get stuck, reach out to the Amazon EMR team by posting on our [Discussion Forum](https://forums.aws.amazon.com/forum.jspa?forumID=52)\.

The sample cluster that you create runs in a live environment\. Charges accrue for cluster instances at the per\-second rate for Amazon EMR pricing\. For more information, see [Amazon EMR Pricing](https://aws.amazon.com/emr/pricing)\. These charges vary by region\. The cost should be minimal because the cluster should run for less than an hour after the cluster is provisioned\.

Charges might also accrue for the storage of query output files that you store in Amazon S3 as part of the tutorial\. The file is small, so charges should be minimal\. Also, if you are within your first year of using AWS, some or all of your charges for Amazon S3 might be waived if you are within the usage limits of the AWS Free Tier\. For more information, see [Amazon S3 Pricing](https://aws.amazon.com/s3/pricing) and [AWS Free Tier](https://aws.amazon.com/free/)\.

If you are considering Amazon EMR in a production capacity, use the [AWS Simple Monthly Calculator](http://calculator.s3.amazonaws.com/calc5.html) to estimate your costs\.

**Topics**
+ [Step 1: Set Up Prerequisites](emr-gs-prerequisites.md)
+ [Step 2: Launch The Cluster](emr-gs-launch-sample-cluster.md)
+ [Step 3: Allow SSH Access](emr-gs-ssh.md)
+ [Step 4: Run a Hive Script to Process Data](emr-gs-process-sample-data.md)
+ [Step 5: Clean Up Resources](emr-gs-reset-environment.md)