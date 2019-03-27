# Instance Type Not Supported<a name="emr-INSTANCE_TYPE_NOT_SUPPORTED-error"></a>

If you create a cluster, and it fails with the error message "The requested instance type *InstanceType* is not supported in the requested Availability Zone," it means that you created the cluster and specified an instance type for one or more instance groups that is not supported by Amazon EMR in the Region and Availability Zone where the cluster was created\. Amazon EMR may support an instance type in one Availability Zone within a Region and not another\. The subnet you select for a cluster determines the Availability Zone within the Region\.

## Solutions<a name="emr-INSTANCE_TYPE_NOT_SUPPORTED-error-solutions"></a>

When you create a cluster using the Amazon EMR console, the list of instances is limited automatically to available instance types, so this error most often occurs when you create a cluster programmatically using the AWS CLI or Amazon EMR API\.

A consolidated list of supported Amazon EMR instance types by Region and Availability Zone is not yet available, so the first step for any solution is to determine whether the instance type you want is available in the Availability Zone you want\.

**To determine available instance types in an Availability Zone using the Amazon EMR management console**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Create cluster**, **Go to advanced options**\.

1. Choose **Next** to view **Hardware Configuration** options\.

1. Choose the **Network** and **EC2 Subnet** for the cluster\.

1. Under **Instance type**, choose the pencil icon next to the default instance type for any of the **Master**, **Core**, or **Task** **Node types**\.

1. A list of instance types available in the Region and Availability Zone associated with the **EC2 Subnet** that you selected is shown\.

1. You can continue creating the cluster, or choose **Cancel**, choose a different **Network** and **EC2 Subnet**, and repeat the previous steps\.

**To determine available instance types in an Availability Zone using the AWS CLI**
+ Use the `ec2 run-instances` command with the `--dry-run` option\. In the example below, replace *m4\.large* with the instance type you want to use, *ami\-035be7bafff33b6b6* with the AMI associated with that instance type, and *subnet\-12ab3c45* with a subnet in the Availability Zone you want to query\.

  `aws ec2 run-instances --instance-type m4.large --dry-run --image-id ami-035be7bafff33b6b6 --subnet-id subnet-12ab3c45`

After you determine the instance types available, you can do any of the following:
+ Create the cluster in the same Region and EC2 Subnet, and choose a different instance type with similar capabilities as your initial choice\. For a list of supported instance types, see [Supported Instance Types](emr-supported-instance-types.md)\. To compare capabilities of EC2 instance types, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.
+ Choose a subnet for the cluster in an Availability Zone where the instance type is available and supported by Amazon EMR\. 