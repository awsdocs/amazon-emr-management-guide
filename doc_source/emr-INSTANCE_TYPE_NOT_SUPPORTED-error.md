# Instance Type Not Supported<a name="emr-INSTANCE_TYPE_NOT_SUPPORTED-error"></a>

If you create a cluster, and it fails with the error message "The requested instance type *InstanceType* is not supported in the requested Availability Zone," it means that you created the cluster and specified an instance type for one or more instance groups that is not supported by Amazon EMR in the Region and Availability Zone where the cluster was created\. Amazon EMR may support an instance type in one Availability Zone within a Region and not another\. The subnet you select for a cluster determines the Availability Zone within the Region\.

## Solution<a name="emr-INSTANCE_TYPE_NOT_SUPPORTED-error-solutions"></a>

**Determine available instance types in an Availability Zone using the AWS CLI**
+ Use the `ec2 run-instances` command with the `--dry-run` option\. In the example below, replace *m5\.xlarge* with the instance type you want to use, *ami\-035be7bafff33b6b6* with the AMI associated with that instance type, and *subnet\-12ab3c45* with a subnet in the Availability Zone you want to query\.

  ```
  aws ec2 run-instances --instance-type m5.xlarge --dry-run --image-id ami-035be7bafff33b6b6 --subnet-id subnet-12ab3c45
  ```

After you determine the instance types available, you can do any of the following:
+ Create the cluster in the same Region and EC2 Subnet, and choose a different instance type with similar capabilities as your initial choice\. For a list of supported instance types, see [Supported Instance Types](emr-supported-instance-types.md)\. To compare capabilities of EC2 instance types, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.
+ Choose a subnet for the cluster in an Availability Zone where the instance type is available and supported by Amazon EMR\. 