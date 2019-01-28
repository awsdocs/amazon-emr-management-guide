# Step 2: Check the Environment<a name="emr-troubleshoot-slow-2"></a>

**Topics**
+ [Check for Service Outages](#emr-troubleshoot-slow-2-outages)
+ [Check Usage Limits](#emr-troubleshoot-slow-2-limits)
+ [Check the Amazon VPC Subnet Configuration](#emr-troubleshoot-slow-2-vpc)
+ [Restart the Cluster](#emr-troubleshoot-slow-2-restart)

## Check for Service Outages<a name="emr-troubleshoot-slow-2-outages"></a>

 Amazon EMR uses several Amazon Web Services internally\. It runs virtual servers on Amazon EC2, stores data and scripts on Amazon S3, indexes log files in Amazon SimpleDB, and reports metrics to CloudWatch\. Events that disrupt these services are rare — but when they occur — can cause issues in Amazon EMR\. 

 Before you go further, check the [Service Health Dashboard](http://status.aws.amazon.com/)\. Check the region where you launched your cluster to see whether there are disruption events in any of these services\. 

## Check Usage Limits<a name="emr-troubleshoot-slow-2-limits"></a>

 If you are launching a large cluster, have launched many clusters simultaneously, or you are an IAM user sharing an AWS account with other users, the cluster may have failed because you exceeded an AWS service limit\. 

 Amazon EC2 limits the number of virtual server instances running on a single AWS region to 20 on\-demand or reserved instances\. If you launch a cluster with more than 20 nodes, or launch a cluster that causes the total number of EC2 instances active on your AWS account to exceed 20, the cluster will not be able to launch all of the EC2 instances it requires and may fail\. When this happens, Amazon EMR returns an `EC2 QUOTA EXCEEDED` error\. You can request that AWS increase the number of EC2 instances that you can run on your account by submitting a [Request to Increase Amazon EC2 Instance Limit](http://aws.amazon.com/contact-us/ec2-request/) application\. 

 Another thing that may cause you to exceed your usage limits is the delay between when a cluster is terminated and when it releases all of its resources\. Depending on its configuration, it may take up to 5\-20 minutes for a cluster to fully terminate and release allocated resources\. If you are getting an `EC2 QUOTA EXCEEDED` error when you attempt to launch a cluster, it may be because resources from a recently terminated cluster may not yet have been released\. In this case, you can either [request that your Amazon EC2 quota be increased](https://aws.amazon.com/contact-us/ec2-request/), or you can wait twenty minutes and re\-launch the cluster\. 

 Amazon S3 limits the number of buckets created on an account to 100\. If your cluster creates a new bucket that exceeds this limit, the bucket creation will fail and may cause the cluster to fail\. 

## Check the Amazon VPC Subnet Configuration<a name="emr-troubleshoot-slow-2-vpc"></a>

If your cluster was launched in a Amazon VPC subnet, the subnet needs to be configured as described in [Configure Networking](emr-plan-vpc-subnet.md)\. In addition, check that the subnet you launch the cluster into has enough free elastic IP addresses to assign one to each node in the cluster\.

## Restart the Cluster<a name="emr-troubleshoot-slow-2-restart"></a>

 The slow down in processing may be caused by a transient condition\. Consider terminating and restarting the cluster to see if performance improves\. 