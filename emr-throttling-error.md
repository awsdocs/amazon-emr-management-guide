# Throttling Errors<a name="emr-throttling-error"></a>

The errors "Throttled from *Amazon EC2* while launching cluster" and "Failed to provision instances due to throttling from *Amazon EC2*" occur when Amazon EMR can not complete a request because another service has throttled the activity\. Amazon EC2 is the most common source of throttling errors, but other services may be the cause of throttling errors\. [AWS service limits](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html) apply on a per\-Region basis to improve performance, and a throttling error indicates that you have exceeded the service limit for your account in that Region\.

## Possible Causes<a name="emr-failed-to-provision-instances-due-to-throttling-causes"></a>

The most common source of Amazon EC2 throttling errors is a large number of cluster instances launching so that your service limit for EC2 instances is exceeded\. Cluster instances may launch for the following reasons:
+ New clusters are created\.
+ Clusters are resized manually\. For more information, see [Manually Resizing a Running Cluster](emr-manage-resize.md)\.
+ Instance groups in a cluster add instances \(scale out\) as a result of an automatic scaling rule\. For more information, see [Understanding Automatic Scaling Rules](emr-automatic-scaling.md#emr-scaling-rules)\.
+ Instance fleets in a cluster add instances to meet an increased target capacity\. For more information, see [Configure Instance Fleets](emr-instance-fleet.md)\.

It is also possible that the frequency or type of API request being made to Amazon EC2 causes throttling errors\. For more information about how Amazon EC2 throttles API requests, see [Query API Request Rate](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/query-api-troubleshooting.html#api-request-rate) in the *Amazon EC2 API Reference*\.

## Solutions<a name="emr-throttling-error-solutions"></a>

Consider the following solutions:
+ [Create a support case](https://console.aws.amazon.com/support/cases#/create) to request a **Service limit increase**\.
+ If you have clusters that launch on the same schedule—for example, at the top of the hour—consider staggering start times\.
+ If you have clusters that are sized for peak demand, and you periodically have instance capacity, consider specifying automatic scaling to add and remove instances on\-demand\. In this way, instances are used more efficiently, and depending on the demand profile, fewer instances may be requested at a given time across an account\. For more information, see [Using Automatic Scaling in Amazon EMR](emr-automatic-scaling.md)\.