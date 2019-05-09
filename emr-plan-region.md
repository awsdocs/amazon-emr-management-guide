# Choose an AWS Region<a name="emr-plan-region"></a>

Amazon Web Services run on servers in data centers around the world\. Data centers are organized by geographical region\. When you launch an Amazon EMR cluster, you must specify a region\. You might choose a region to reduce latency, minimize costs, or address regulatory requirements\. For the list of regions and endpoints supported by Amazon EMR, see [Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/#emr_region) in the *Amazon Web Services General Reference*\. 

For best performance, you should launch the cluster in the same region as your data\. For example, if the Amazon S3 bucket storing your input data is in the US West \(Oregon\) region, you should launch your cluster in the US West \(Oregon\) region to avoid cross\-region data transfer fees\. If you use an Amazon S3 bucket to receive the output of the cluster, you would also want to create it in the US West \(Oregon\) region\. 

If you plan to associate an Amazon EC2 key pair with the cluster \(required for using SSH to log on to the master node\), the key pair must be created in the same region as the cluster\. Similarly, the security groups that Amazon EMR creates to manage the cluster are created in the same region as the cluster\. 

If you signed up for an AWS account on or after May 17, 2017, the default region when you access a resource from the AWS Management Console is US East \(Ohio\) \(us\-east\-2\); for older accounts, the default region is either US West \(Oregon\) \(us\-west\-2\) or US East \(N\. Virginia\) \(us\-east\-1\)\. For more information, see [Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html)\. 

Some AWS features are available only in limited regions\. For example, Cluster Compute instances are available only in the US East \(N\. Virginia\) region, and the Asia Pacific \(Sydney\) region supports only Hadoop 1\.0\.3 and later\. When choosing a region, check that it supports the features you want to use\.

For best performance, use the same region for all of your AWS resources that will be used with the cluster\. The following table maps the region names between services\. For a list of Amazon EMR regions, see [AWS Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#emr_region) in the *Amazon Web Services General Reference*\.

## Choose a Region Using the Console<a name="emr-dev-specify-region-console"></a>

Your default region is displayed automatically\.

**To change regions using the console**
+ To switch regions, choose the region list to the right of your account information on the navigation bar\.

## Specify a Region Using the AWS CLI<a name="emr-dev-specify-region-cli"></a>

You specify a default region in the AWS CLI using either the aws configure command or the `AWS_DEFAULT_REGION` environment variable\. For more information, see [Configuring the AWS Region](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html#cli-installing-specifying-region) in the *AWS Command Line Interface User Guide*\.

## Choose a Region Using an SDK or the API<a name="emr-dev-specify-region-api"></a>

To choose a region using an SDK, configure your application to use that region's endpoint\. If you are creating a client application using an AWS SDK, you can change the client endpoint by calling `setEndpoint`, as shown in the following example:

```
1. client.setEndpoint("elasticmapreduce.us-west-2.amazonaws.com");
```

After your application has specified a region by setting the endpoint, you can set the Availability Zone for your cluster's EC2 instances\. Availability Zones are distinct geographical locations that are engineered to be insulated from failures in other Availability Zones and provide inexpensive, low latency network connectivity to other Availability Zones in the same region\. A region contains one or more Availability Zones\. To optimize performance and reduce latency, all resources should be located in the same Availability Zone as the cluster that uses them\. 