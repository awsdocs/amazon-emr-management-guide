# EC2 is Out of Capacity<a name="emr-EC2_INSUFFICIENT_CAPACITY-error"></a>

An "EC2 is out of capacity for *InstanceType*" error occurs when a cluster is created, or instances are added to a cluster, and there are no more of that EC2 instance type in the Region or Availability Zone because of demand\. The subnet that you select for a cluster determines the Availability Zone\.

If you are creating a cluster, you can specify a different instance type with similar capabilities or create the cluster in a different Region or select a subnet in an Availability Zone where the instance type you want might be available\.

If instances are being added to a running cluster, you can modify instance group configurations or instance fleet configurations to add available instance types with similar capabilities\. For a list of supported instance types, see [Supported Instance Types](emr-supported-instance-types.md)\. To compare capabilities of EC2 instance types, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\. You can also terminate the cluster and recreate it in a Region and Availability Zone where the instance type is available\.